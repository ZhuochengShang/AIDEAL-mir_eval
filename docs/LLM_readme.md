# mir_eval — LLM_readme

LLM-facing API documentation. Generated from the API surface, project profile, and distilled docs.

## API Test: `IntervalFormatter`

### Signature
```python
class IntervalFormatter(Formatter)
```
_Source: source/mir_eval/display.py:478_

_Source doc:_ Ticker formatter for labeled interval plots. Parameters ---------- base : array-like of int The base positions of each label ticks : array-like of string The labels for the ticks

### Goal
Provides a custom Matplotlib ticker formatter to display specific text labels at defined interval positions on a plot axis.

### Parameters
_None._

### Input
The class is initialized with two parameters:
*   **base**: `array-like` of `int`, representing the coordinate positions where labels should be placed.
*   **ticks**: `array-like` of `string`, representing the corresponding labels to display at each position defined in `base`.

### Output
Returns an instance of `IntervalFormatter`, which acts as a callable object compatible with Matplotlib's `ax.xaxis.set_major_formatter` or `ax.yaxis.set_major_formatter`.

### Valid Call Patterns
```python
# Inferred from signature:
import mir_eval.display
import matplotlib.pyplot as plt

formatter = mir_eval.display.IntervalFormatter(base=[0, 10, 20], ticks=['A', 'B', 'C'])
ax = plt.gca()
ax.xaxis.set_major_formatter(formatter)
```

### LLM Instruction Prompt
- When using `IntervalFormatter`, ensure the `base` array and `ticks` array are of equal length.
- The object must be passed to a Matplotlib axis formatter method (e.g., `set_major_formatter`).
- This class is intended for use within `mir_eval.display` workflows for visualizing interval-based annotations.

### Prompt Snippet
```text
# Example of setting up an IntervalFormatter for a plot
formatter = mir_eval.display.IntervalFormatter(base=my_positions, ticks=my_labels)
ax.xaxis.set_major_formatter(formatter)
```

### Common Failure Modes
- **Length Mismatch**: Providing `base` and `ticks` arrays of different lengths will cause errors during the rendering phase when Matplotlib attempts to map positions to labels.
- **Type Errors**: Passing non-integer values to `base` or non-string values to `ticks` may result in unexpected formatting behavior or exceptions within the Matplotlib backend.
- **Missing Context**: Attempting to use the formatter without an active Matplotlib axis or before the plot has been initialized.

### Fix Code Hint
```python
# Ensure arrays are aligned and of equal length before initialization
import numpy as np
base = np.array([0, 5, 10])
ticks = np.array(['Start', 'Middle', 'End'])
assert len(base) == len(ticks), "Base and ticks must have the same length"
formatter = mir_eval.display.IntervalFormatter(base, ticks)
```

## API Test: `InvalidChordException`

### Signature
```python
class InvalidChordException(Exception)
```
_Source: source/mir_eval/chord.py:113_

_Source doc:_ Exception class for suspect / invalid chord labels

### Goal
Provides a specific exception type raised by the `mir_eval.chord` submodule when encountering malformed or unrecognized chord label strings during evaluation or processing.

### Parameters
_None._

### Input
The exception is raised internally by `mir_eval` functions when processing chord labels that do not conform to the expected syntax (e.g., standard chord notation used in MIR datasets). Users do not provide inputs to the class constructor directly in standard workflows, but must ensure their input annotations are formatted correctly to avoid triggering this exception.

### Output
Returns `unspecified` — This is an exception class; it does not return a value but instead interrupts execution when raised to signal that a chord label could not be parsed.

### Valid Call Patterns
```python
# This exception is typically raised by the library, but can be caught as follows:
try:
    # Example: processing chord labels
    pass
except mir_eval.chord.InvalidChordException as e:
    print(f"Caught an invalid chord label: {e}")
```
*(Note: This call pattern is inferred from the class signature as no explicit test-suite usage was provided.)*

### LLM Instruction Prompt
- When implementing code that processes chord labels using `mir_eval`, always wrap the parsing or evaluation logic in a `try-except` block targeting `mir_eval.chord.InvalidChordException` to handle malformed annotation files gracefully.

### Prompt Snippet
```text
import mir_eval
try:
    # Perform chord evaluation or label parsing
    ...
except mir_eval.chord.InvalidChordException:
    # Handle the error
    ...
```

### Common Failure Modes
- **Malformed Annotations:** Providing chord labels in a text file that do not adhere to the standard MIR chord syntax expected by the library.
- **Unexpected Delimiters:** Using non-standard separators in annotation files that cause the parser to misinterpret the chord string, leading to an invalid label exception.

### Fix Code Hint
```python
# Ensure your annotation files follow the standard MIR chord format
# and validate strings before passing them to mir_eval functions.
import re

def is_valid_chord(label):
    # Basic validation logic before calling mir_eval
    return bool(re.match(r'^[A-G][b#]?:.*', label))
```

## API Test: `absolute_error`

### Signature
```python
def absolute_error(reference_timestamps, estimated_timestamps)
```
_Source: source/mir_eval/alignment.py:115_

_Source doc:_ Compute the absolute deviations between estimated and reference timestamps, and then returns the median and average over all events Examples -------- >>> reference_timestamps = mir_eval.io.load_events('reference.txt') >>> estimated_timestamps = mir_eval.io.load_events('estimated.txt') >>> mae, aae = mir_eval.align.absolute_error(reference_onsets, estimated_timestamps) Parameters ---------- reference_timestamps : np.ndarray reference timestamps, in seconds estimated_timestamps : np.ndarray estimated timestamps, in seconds Returns ------- mae : float Median absolute error aae: float Average absolute error

### Goal
Computes the median and average absolute deviations between estimated and reference timestamps to evaluate the temporal accuracy of event detection.

### Parameters
- `reference_timestamps`: A `np.ndarray` containing the ground-truth event timestamps in seconds.
- `estimated_timestamps`: A `np.ndarray` containing the estimated event timestamps in seconds.

### Input
The function expects two `np.ndarray` objects representing time points in seconds. These are typically loaded using `mir_eval.io.load_events`. Precondition: The input arrays must be aligned or matched such that the absolute difference is meaningful for the specific evaluation task.

### Output
Returns a tuple of two floats:
- `mae`: The median absolute error.
- `aae`: The average absolute error.

### Valid Call Patterns
```python
import mir_eval
# Example inferred from documentation and signature
reference_timestamps = mir_eval.io.load_events('reference.txt')
estimated_timestamps = mir_eval.io.load_events('estimated.txt')
mae, aae = mir_eval.align.absolute_error(reference_timestamps, estimated_timestamps)
```

### LLM Instruction Prompt
- When calling `mir_eval.align.absolute_error`, ensure both `reference_timestamps` and `estimated_timestamps` are provided as `np.ndarray` objects.
- Always assign the result to a tuple of two variables: `(mae, aae)`.
- Ensure inputs are in seconds, as per the library's standard for temporal annotations.

### Prompt Snippet
```text
mae, aae = mir_eval.align.absolute_error(reference_timestamps, estimated_timestamps)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays of different lengths or incompatible dimensions may cause errors during the calculation of deviations.
- **Unit Mismatch:** Providing timestamps in milliseconds instead of seconds will result in incorrect error metrics.
- **Unsorted Inputs:** If the event lists are not chronologically ordered, the resulting absolute errors may not reflect the intended temporal alignment.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays and sorted chronologically
reference_timestamps = np.sort(np.array(reference_timestamps))
estimated_timestamps = np.sort(np.array(estimated_timestamps))
mae, aae = mir_eval.align.absolute_error(reference_timestamps, estimated_timestamps)
```

## API Test: `adjust_events`

### Signature
```python
def adjust_events(events, labels=None, t_min=0.0, t_max=None, label_prefix='__')
```
_Source: source/mir_eval/util.py:359_

_Source doc:_ Adjust the given list of event times to span the range ``[t_min, t_max]``. Any event times outside of the specified range will be removed. If the times do not span ``[t_min, t_max]``, additional events will be added with the prefix ``label_prefix``. Parameters ---------- events : np.ndarray Array of event times (seconds) labels : list or None List of labels (Default value = None) t_min : float or None Minimum valid event time. (Default value = 0.0) t_max : float or None Maximum valid event time. (Default value = None) label_prefix : str Prefix string to use for synthetic labels (Default value = '__') Returns ------- new_times : np.ndarray Event times corrected to the given range.

### Goal
Standardizes event time series by trimming outliers and padding boundaries to ensure the data strictly spans the requested `[t_min, t_max]` interval.

### Parameters
- `events`: A NumPy array of event times in seconds.
- `labels`, default `None`: A list of labels corresponding to the input `events`. If provided, the list length must match the number of input events.
- `t_min`, default `0.0`: The minimum valid time boundary in seconds.
- `t_max`, default `None`: The maximum valid time boundary in seconds.
- `label_prefix`, default `'__'`: The prefix string used for labeling synthetic events created when padding to `t_min` or `t_max`.

### Input
- **Data:** `events` must be a 1D NumPy array of floats.
- **Preconditions:** If `labels` is provided, it must be a list of the same length as `events`. The function assumes time values are in seconds.

### Output
Returns a tuple containing:
- `new_times`: A NumPy array of adjusted event times.
- `new_labels`: A list of labels corresponding to the adjusted event times.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Standard usage with trimming and padding
events = np.array([1.0, 5.0, 10.0])
labels = ['a', 'b', 'c']
new_e, new_l = mir_eval.util.adjust_events(events, labels, t_min=0.0, t_max=11.0)
```

### LLM Instruction Prompt
- When calling `adjust_events`, always ensure `events` is a NumPy array.
- If `labels` are provided, ensure the length of the list matches the length of the `events` array to avoid index errors.
- Note that the function returns both the adjusted times and the adjusted labels; ensure your code captures both return values.

### Prompt Snippet
```text
# Adjusting event times to a specific range
new_times, new_labels = mir_eval.util.adjust_events(events, labels=labels, t_min=0.0, t_max=t_max)
```

### Common Failure Modes
- **Length Mismatch:** Passing a `labels` list that does not match the length of the `events` array will result in an error.
- **Type Errors:** Passing a list instead of a NumPy array for `events` may cause unexpected behavior in downstream processing.
- **Empty Input:** If `events` is empty, the function will still attempt to pad to `t_min` and `t_max`, which may result in a list containing only the boundary events.

### Fix Code Hint
```python
# Ensure events are a numpy array before passing
events = np.array(events)
# Ensure labels are provided if you want to maintain metadata
new_e, new_l = mir_eval.util.adjust_events(events, labels=labels, t_min=0.0, t_max=10.0)
```

## API Test: `adjust_intervals`

### Signature
```python
def adjust_intervals(intervals, labels=None, t_min=0.0, t_max=None, start_label='__T_MIN', end_label='__T_MAX')
```
_Source: source/mir_eval/util.py:261_

_Source doc:_ Adjust a list of time intervals to span the range ``[t_min, t_max]``. Any intervals lying completely outside the specified range will be removed. Any intervals lying partially outside the specified range will be cropped. If the specified range exceeds the span of the provided data in either direction, additional intervals will be appended.  If an interval is appended at the beginning, it will be given the label ``start_label``; if an interval is appended at the end, it will be given the label ``end_label``. Parameters ---------- intervals : np.ndarray, shape=(n_events, 2) Array of interval start and end-times labels : list, len=n_events or None List of labels (Default value = None) t_min : float or None Minimum interval start time. (Default value = 0.0) t_max : float or None Maximum interval end time. (Default value = None) start_label : str or float or int Label to give any intervals appended at the beginning (Default value = '__T_MIN') end_label : str or float or int Label to give any intervals appended at the end (Default value = '__T_MAX') Returns ------- new_intervals : np.ndarray Intervals spanning ``[t_min, t_max]`` new_labels : list List of labels for ``new_labels``

### Goal
Standardizes a set of temporal intervals to a specific time range by cropping, removing, or padding data to ensure the output spans exactly `[t_min, t_max]`.

### Parameters
- `intervals`: A NumPy array of shape `(n_events, 2)` containing the start and end times for each event.
- `labels`, default `None`: A list of labels corresponding to each interval; if provided, must have length `n_events`.
- `t_min`, default `0.0`: The desired start time for the interval set.
- `t_max`, default `None`: The desired end time for the interval set.
- `start_label`, default `'__T_MIN'`: The label assigned to any padding interval added at the beginning of the range.
- `end_label`, default `'__T_MAX'`: The label assigned to any padding interval added at the end of the range.

### Input
- **Data:** `intervals` must be a NumPy array of shape `(n_events, 2)`.
- **Preconditions:** If `labels` is provided, it must be a list of length `n_events`. The function assumes time values are floats.

### Output
Returns a tuple containing:
- `new_intervals`: A NumPy array of intervals spanning the range `[t_min, t_max]`.
- `new_labels`: A list of labels corresponding to the adjusted intervals.

### Valid Call Patterns
```python
# Inferred from signature:
import mir_eval.util
new_intervals, new_labels = mir_eval.util.adjust_intervals(intervals, labels=labels, t_min=0.0, t_max=10.0)
```

### LLM Instruction Prompt
- When calling `adjust_intervals`, ensure `intervals` is a NumPy array of shape `(n_events, 2)`.
- If `labels` are provided, ensure the list length matches the number of rows in `intervals`.
- Use this function to normalize segment or event data before passing it to evaluation metrics that require a fixed temporal window.

### Prompt Snippet
```text
from mir_eval.util import adjust_intervals
# Adjust intervals to a specific range [t_min, t_max]
new_intervals, new_labels = adjust_intervals(intervals, labels=labels, t_min=t_min, t_max=t_max)
```

### Common Failure Modes
- **Shape Mismatch:** Passing an `intervals` array that is not `(n_events, 2)` will cause downstream errors.
- **Label Mismatch:** Providing a `labels` list that does not match the number of intervals will result in an index or length error.
- **Invalid Time Range:** If `t_max` is less than `t_min`, the resulting intervals may be logically inconsistent.

### Fix Code Hint
```python
import numpy as np
# Ensure intervals are a numpy array
intervals = np.array(intervals)
# Ensure labels match the number of intervals
if labels is not None and len(labels) != len(intervals):
    raise ValueError("Labels must have the same length as intervals.")
```

## API Test: `ari`

### Signature
```python
def ari(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1)
```
_Source: source/mir_eval/segment.py:589_

_Source doc:_ Compute the Adjusted Rand Index (ARI) for frame clustering segmentation evaluation. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> ari_score = mir_eval.structure.ari(ref_intervals, ref_labels, ...                                    est_intervals, est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) Returns ------- ari_score : float > 0 Adjusted Rand index between segmentations.

### Goal
Computes the Adjusted Rand Index (ARI) to evaluate the similarity between a reference and an estimated music segmentation by clustering the audio into frames.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). The ground-truth segment intervals.
- `reference_labels`: `list`, shape=(n,). The ground-truth segment labels.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). The estimated segment intervals.
- `estimated_labels`: `list`, shape=(m,). The estimated segment labels.
- `frame_size`, default `0.1`: `float > 0`. The duration in seconds for each frame used in the clustering evaluation.

### Input
Inputs must be provided as NumPy arrays for intervals and lists for labels, typically obtained via `mir_eval.io.load_labeled_intervals`. Precondition: The estimated intervals must be trimmed or padded to match the reference timing using `mir_eval.util.adjust_intervals` before calling this function to ensure temporal alignment.

### Output
Returns `ari_score` (float > 0), representing the Adjusted Rand Index, which measures the agreement between the two segmentations, adjusted for chance.

### Valid Call Patterns
```python
import mir_eval
# Inferred from documentation examples
ari_score = mir_eval.structure.ari(ref_intervals, ref_labels, est_intervals, est_labels)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are aligned using `mir_eval.util.adjust_intervals` before passing them to `ari`.
- Ensure `frame_size` is a positive float.
- Use `mir_eval.io.load_labeled_intervals` to prepare the input data.

### Prompt Snippet
```text
# Ensure temporal alignment before computing ARI
ref_intervals, ref_labels = mir_eval.util.adjust_intervals(ref_intervals, ref_labels, t_min=0)
est_intervals, est_labels = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
ari_score = mir_eval.structure.ari(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- Mismatched temporal boundaries: Failing to use `mir_eval.util.adjust_intervals` to align the estimated segmentation with the reference will lead to errors or invalid results.
- Invalid `frame_size`: Providing a non-positive value for `frame_size` will violate the function's constraints.
- Incorrect input types: Passing raw file paths instead of the loaded NumPy arrays/lists returned by `mir_eval.io` loaders.

### Fix Code Hint
```python
# If intervals are not aligned, use:
est_intervals, est_labels = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
```

## API Test: `average_overlap_ratio`

### Signature
```python
def average_overlap_ratio(ref_intervals, est_intervals, matching)
```
_Source: source/mir_eval/transcription.py:591_

_Source doc:_ Compute the Average Overlap Ratio between a reference and estimated note transcription. Given a reference and corresponding estimated note, their overlap ratio (OR) is defined as the ratio between the duration of the time segment in which the two notes overlap and the time segment spanned by the two notes combined (earliest onset to latest offset): >>> OR = ((min(ref_offset, est_offset) - max(ref_onset, est_onset)) / ...     (max(ref_offset, est_offset) - min(ref_onset, est_onset))) The Average Overlap Ratio (AOR) is given by the mean OR computed over all matching reference and estimated notes. The metric goes from 0 (worst) to 1 (best). Note: this function assumes the matching of reference and estimated notes (see :func:`match_notes`) has already been performed and is provided by the ``matching`` parameter. Furthermore, it is highly recommended to validate the intervals (see :func:`validate_intervals`) before calling this function, otherwise it is possible (though unlikely) for this function to attempt a divide-by-zero operation. Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) matching : list of tuples A list of matched reference and estimated notes. ``matching[i] == (i, j)`` where reference note ``i`` matches estimated note ``j``. Returns ------- avg_overlap_ratio : float The computed Average Overlap Ratio score

### Goal
Computes the mean overlap ratio between matched reference and estimated note intervals to evaluate the temporal precision of a transcription system.

### Parameters
- `ref_intervals`: `np.ndarray`, shape=(n,2). An array containing the onset and offset times for each reference note.
- `est_intervals`: `np.ndarray`, shape=(m,2). An array containing the onset and offset times for each estimated note.
- `matching`: `list of tuples`. A list of index pairs `(i, j)` where `ref_intervals[i]` is matched to `est_intervals[j]`.

### Input
The function requires two NumPy arrays of shape `(N, 2)` representing time intervals. It is a strict precondition that the `matching` list has been generated beforehand (typically via `mir_eval.transcription.match_notes`). It is highly recommended to run `mir_eval.util.validate_intervals` on both input arrays prior to calling this function to ensure valid temporal data and prevent potential divide-by-zero errors.

### Output
Returns `float`: The Average Overlap Ratio (AOR), a scalar value ranging from 0 (worst) to 1 (best).

### Valid Call Patterns
```python
# Inferred from signature and documentation
import numpy as np
from mir_eval.transcription import average_overlap_ratio

# Assuming ref_intervals, est_intervals, and matching are pre-computed
aor = average_overlap_ratio(ref_intervals, est_intervals, matching)
```

### LLM Instruction Prompt
- Always ensure `ref_intervals` and `est_intervals` are `np.ndarray` objects with two columns (onset, offset).
- The `matching` parameter must be a list of tuples where each tuple contains valid indices for the respective input arrays.
- Always validate intervals using `mir_eval.util.validate_intervals` before passing them to this function.

### Prompt Snippet
```text
# Example of calculating AOR after matching
matching = mir_eval.transcription.match_notes(ref_intervals, est_intervals)
aor = mir_eval.transcription.average_overlap_ratio(ref_intervals, est_intervals, matching)
```

### Common Failure Modes
- **Divide-by-zero:** Occurs if intervals are invalid (e.g., offset <= onset). Always run `validate_intervals` first.
- **IndexError:** Occurs if the indices provided in the `matching` list are out of bounds for the provided `ref_intervals` or `est_intervals` arrays.
- **Empty Matching:** If the `matching` list is empty, the function may fail or return an undefined result; ensure the matching logic handles empty sets gracefully.

### Fix Code Hint
```python
import mir_eval
# Validate before processing
mir_eval.util.validate_intervals(ref_intervals)
mir_eval.util.validate_intervals(est_intervals)
# Proceed with calculation
aor = mir_eval.transcription.average_overlap_ratio(ref_intervals, est_intervals, matching)
```

## API Test: `boundaries_to_intervals`

### Signature
```python
def boundaries_to_intervals(boundaries)
```
_Source: source/mir_eval/util.py:239_

_Source doc:_ Convert an array of event times into intervals Parameters ---------- boundaries : list-like List-like of event times.  These are assumed to be unique timestamps in ascending order. Returns ------- intervals : np.ndarray, shape=(n_intervals, 2) Start and end time for each interval

### Goal
Converts a sequence of discrete event timestamps into a set of contiguous time intervals, typically used for segment or structural analysis in music information retrieval.

### Parameters
- `boundaries`: A list-like object containing event times. These must be unique timestamps provided in strictly ascending order.

### Input
The input must be a list-like structure (e.g., a NumPy array or Python list) of floats representing time in seconds. Precondition: The timestamps must be unique and sorted in ascending order to ensure valid interval generation.

### Output
Returns `np.ndarray` with shape `(n_intervals, 2)`, where each row contains the start and end time for an interval derived from the input boundaries.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example derived from test suite
boundaries = np.arange(10)
intervals = mir_eval.util.boundaries_to_intervals(boundaries)
```

### LLM Instruction Prompt
- When calling `mir_eval.util.boundaries_to_intervals`, ensure the input `boundaries` is a sorted, unique sequence of timestamps. If the data is not sorted, apply `np.sort()` before passing it to the function.

### Prompt Snippet
```text
# Ensure boundaries are sorted before conversion
sorted_boundaries = np.sort(boundaries)
intervals = mir_eval.util.boundaries_to_intervals(sorted_boundaries)
```

### Common Failure Modes
- **Unsorted Input:** Providing timestamps that are not in ascending order will result in intervals with negative durations or incorrect temporal logic.
- **Duplicate Timestamps:** Including duplicate timestamps violates the assumption of unique event times and may lead to zero-length intervals that are invalid for downstream evaluation tasks.
- **Non-list-like Input:** Passing non-iterable types or objects that cannot be converted to a NumPy array will raise a `TypeError`.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a unique, sorted numpy array
boundaries = np.unique(np.array(boundaries))
intervals = mir_eval.util.boundaries_to_intervals(boundaries)
```

## API Test: `bss_eval_images`

### Signature
```python
def bss_eval_images(reference_sources, estimated_sources, compute_permutation=True)
```
_Source: source/mir_eval/separation.py:372_

_Source doc:_ Compute the bss_eval_images function from the BSS_EVAL Matlab toolbox. Ordering and measurement of the separation quality for estimated source signals in terms of filtered true source, interference and artifacts. This method also provides the ISR measure. The decomposition allows a time-invariant filter distortion of length 512, as described in Section III.B of [#vincent2006performance]_. Passing ``False`` for ``compute_permutation`` will improve the computation performance of the evaluation; however, it is not always appropriate and is not the way that the BSS_EVAL Matlab toolbox computes bss_eval_images. Examples -------- >>> # reference_sources[n] should be an ndarray of samples of the >>> # n'th reference source >>> # estimated_sources[n] should be the same for the n'th estimated >>> # source >>> (sdr, isr, sir, sar, ...  perm) = mir_eval.separation.bss_eval_images(reference_sources, ...                                               estimated_sources) Parameters ---------- reference_sources : np.ndarray, shape=(nsrc, nsampl, nchan) matrix containing true sources estimated_sources : np.ndarray, shape=(nsrc, nsampl, nchan) matrix containing estimated sources compute_permutation : bool, optional compute permutation of estimate/source combinations (True by default) Returns ------- sdr : np.ndarray, shape=(nsrc,) vector of Signal to Distortion Ratios (SDR) isr : np.ndarray, shape=(nsrc,) vector of source Image to Spatial distortion Ratios (ISR) sir : np.ndarray, shape=(nsrc,) vector of Source to Interference Ratios (SIR) sar : np.ndarray, shape=(nsrc,) vector of Sources to Artifacts Ratios (SAR) perm : np.ndarray, shape=(nsrc,) vector containing the best ordering of estimated sources in the mean SIR sense (estimated source number ``perm[j]`` corresponds to true source number ``j``).  Note: ``perm`` will be ``(1,2,...,nsrc)`` if ``compute_permutation`` is ``False``. References ---------- .. [#] Emmanuel Vincent, Shoko Araki, Fabian J. Theis, Guido Nolte, Pau Bofill, Hiroshi Sawada, Alexey Ozerov, B. Vikrham Gowreesunker, Dominik Lutter and Ngoc Q.K. Duong, "The Signal Separation Evaluation Campaign (2007-2010): Achievements and remaining challenges", Signal Processing, 92, pp. 1928-1936, 2012.

### Goal
Evaluates the quality of source separation by decomposing estimated signals into components of true source, interference, and artifacts, allowing for time-invariant filter distortion.

### Parameters
- `reference_sources`: A NumPy array of shape `(nsrc, nsampl, nchan)` containing the ground-truth source signals.
- `estimated_sources`: A NumPy array of shape `(nsrc, nsampl, nchan)` containing the estimated source signals.
- `compute_permutation`, default `True`: If `True`, calculates the optimal permutation of estimated sources to match reference sources based on mean SIR; if `False`, assumes the input order is already matched.

### Input
- **Data:** Requires two 3D NumPy arrays representing multi-channel audio signals.
- **Preconditions:** Both arrays must have identical dimensions `(nsrc, nsampl, nchan)`. The `separation` module is deprecated; users should be aware of potential future removal.

### Output
Returns a tuple containing:
- `sdr`: `np.ndarray` of shape `(nsrc,)` (Signal to Distortion Ratios).
- `isr`: `np.ndarray` of shape `(nsrc,)` (Image to Spatial distortion Ratios).
- `sir`: `np.ndarray` of shape `(nsrc,)` (Source to Interference Ratios).
- `sar`: `np.ndarray` of shape `(nsrc,)` (Sources to Artifacts Ratios).
- `perm`: `np.ndarray` of shape `(nsrc,)` (The optimal permutation mapping).

### Valid Call Patterns
```python
import mir_eval
# Standard usage
sdr, isr, sir, sar, perm = mir_eval.separation.bss_eval_images(ref, est)

# Performance-optimized usage
sdr, isr, sir, sar, perm = mir_eval.separation.bss_eval_images(ref, est, compute_permutation=False)
```

### LLM Instruction Prompt
- Always ensure `reference_sources` and `estimated_sources` are 3D NumPy arrays with matching dimensions.
- Note that this function is part of the `separation` module, which is currently marked as deprecated.
- When `compute_permutation` is `False`, the returned `perm` will be a simple sequence `(0, 1, ..., nsrc-1)`.

### Prompt Snippet
```text
mir_eval.separation.bss_eval_images(reference_sources, estimated_sources, compute_permutation=True)
```

### Common Failure Modes
- **Dimension Mismatch:** Passing arrays where `nsrc` or `nchan` differ between reference and estimate will raise a `ValueError`.
- **Silent Inputs:** Providing silent or empty arrays can trigger `ValueError` or `UserWarning` depending on the input state.
- **Memory Usage:** For very long signals, the internal filter estimation (length 512) may be computationally expensive.

### Fix Code Hint
```python
# Ensure inputs are 3D arrays before calling
import numpy as np
ref = np.atleast_3d(reference_sources)
est = np.atleast_3d(estimated_sources)
sdr, isr, sir, sar, perm = mir_eval.separation.bss_eval_images(ref, est)
```

## API Test: `bss_eval_images_framewise`

### Signature
```python
def bss_eval_images_framewise(reference_sources, estimated_sources, window=30 * 44100, hop=15 * 44100, compute_permutation=False)
```
_Source: source/mir_eval/separation.py:507_

_Source doc:_ Framewise computation of bss_eval_images Please be aware that this function does not compute permutations (by default) on the possible relations between ``reference_sources`` and ``estimated_sources`` due to the dangers of a changing permutation. Therefore (by default), it assumes that ``reference_sources[i]`` corresponds to ``estimated_sources[i]``. To enable computing permutations please set ``compute_permutation`` to be ``True`` and check that the returned ``perm`` is identical for all windows. NOTE: if ``reference_sources`` and ``estimated_sources`` would be evaluated using only a single window or are shorter than the window length, the result of ``bss_eval_images`` called on ``reference_sources`` and ``estimated_sources`` (with the ``compute_permutation`` parameter passed to ``bss_eval_images``) is returned Examples -------- >>> # reference_sources[n] should be an ndarray of samples of the >>> # n'th reference source >>> # estimated_sources[n] should be the same for the n'th estimated >>> # source >>> (sdr, isr, sir, sar, ...  perm) = mir_eval.separation.bss_eval_images_framewise( reference_sources, ...      estimated_sources, window, ....     hop) Parameters ---------- reference_sources : np.ndarray, shape=(nsrc, nsampl, nchan) matrix containing true sources (must have the same shape as ``estimated_sources``) estimated_sources : np.ndarray, shape=(nsrc, nsampl, nchan) matrix containing estimated sources (must have the same shape as ``reference_sources``) window : int Window length for framewise evaluation hop : int Hop size for framewise evaluation compute_permutation : bool, optional compute permutation of estimate/source combinations for all windows (False by default) Returns ------- sdr : np.ndarray, shape=(nsrc, nframes) vector of Signal to Distortion Ratios (SDR) isr : np.ndarray, shape=(nsrc, nframes) vector of source Image to Spatial distortion Ratios (ISR) sir : np.ndarray, shape=(nsrc, nframes) vector of Source to Interference Ratios (SIR) sar : np.ndarray, shape=(nsrc, nframes) vector of Sources to Artifacts Ratios (SAR) perm : np.ndarray, shape=(nsrc, nframes) vector containing the best ordering of estimated sources in the mean SIR sense (estimated source number perm[j] corresponds to true source number j) Note: perm will be range(nsrc) for all windows if compute_permutation

### Goal
Computes BSS_EVAL metrics (SDR, ISR, SIR, SAR) for source separation outputs in a sliding-window, framewise manner to track performance variations over time.

### Parameters
- `reference_sources`: `np.ndarray` of shape `(nsrc, nsampl, nchan)` containing the ground-truth audio sources.
- `estimated_sources`: `np.ndarray` of shape `(nsrc, nsampl, nchan)` containing the estimated audio sources. Must match the shape of `reference_sources`.
- `window`, default `30 * 44100`: `int` representing the window length in samples for the evaluation.
- `hop`, default `15 * 44100`: `int` representing the hop size in samples between windows.
- `compute_permutation`, default `False`: `bool` indicating whether to solve the permutation problem for each window. If `False`, assumes `reference_sources[i]` corresponds to `estimated_sources[i]`.

### Input
- **Data:** Requires two 3D NumPy arrays (`nsrc` sources, `nsampl` samples, `nchan` channels).
- **Preconditions:** Both arrays must have identical shapes. If the input arrays are empty, the function will issue a `UserWarning` and return empty arrays.
- **Validation:** The function performs internal validation; providing mismatched shapes or silent sources where expected will raise a `ValueError`.

### Output
Returns a tuple of five `np.ndarray` objects: `(sdr, isr, sir, sar, perm)`. Each array has shape `(nsrc, nframes)`, where `nframes` is the number of windows processed. `perm` contains the best-matching source indices if `compute_permutation` is `True`, otherwise it defaults to `range(nsrc)`.

### Valid Call Patterns
```python
import mir_eval
# Inferred from signature and test suite usage
(sdr, isr, sir, sar, perm) = mir_eval.separation.bss_eval_images_framewise(
    reference_sources, 
    estimated_sources, 
    window=44100, 
    hop=22050, 
    compute_permutation=True
)
```

### LLM Instruction Prompt
- Always ensure `reference_sources` and `estimated_sources` are 3D NumPy arrays with matching dimensions.
- If `compute_permutation` is set to `True`, verify that the returned `perm` is consistent across windows, as changing permutations can lead to misleading temporal trends.
- Use the default window/hop values only if the audio sampling rate is 44.1kHz and the default 30s/15s analysis is appropriate for the signal length.

### Prompt Snippet
```text
mir_eval.separation.bss_eval_images_framewise(reference_sources, estimated_sources, window=..., hop=..., compute_permutation=...)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays where `nsrc` or `nchan` differ between reference and estimate will raise a `ValueError`.
- **Silent Sources:** Providing silent sources in the input arrays can trigger `ValueError` during the internal BSS_EVAL calculation.
- **Permutation Instability:** When `compute_permutation=True`, the permutation may flip between windows, making it difficult to interpret the temporal evolution of metrics for a specific source.

### Fix Code Hint
```python
# Ensure inputs are 3D and match
if reference_sources.shape != estimated_sources.shape:
    raise ValueError("Reference and estimated sources must have the same shape.")
# Handle empty inputs to avoid warnings
if reference_sources.size == 0:
    return np.array([]), np.array([]), np.array([]), np.array([]), np.array([])
```

## API Test: `bss_eval_sources`

### Signature
```python
def bss_eval_sources(reference_sources, estimated_sources, compute_permutation=True)
```
_Source: source/mir_eval/separation.py:149_

_Source doc:_ Ordering and measurement of the separation quality for estimated source signals in terms of filtered true source, interference and artifacts. The decomposition allows a time-invariant filter distortion of length 512, as described in Section III.B of [#vincent2006performance]_. Passing ``False`` for ``compute_permutation`` will improve the computation performance of the evaluation; however, it is not always appropriate and is not the way that the BSS_EVAL Matlab toolbox computes bss_eval_sources. Examples -------- >>> # reference_sources[n] should be an ndarray of samples of the >>> # n'th reference source >>> # estimated_sources[n] should be the same for the n'th estimated >>> # source >>> (sdr, sir, sar, ...  perm) = mir_eval.separation.bss_eval_sources(reference_sources, ...                                               estimated_sources) Parameters ---------- reference_sources : np.ndarray, shape=(nsrc, nsampl) matrix containing true sources (must have same shape as estimated_sources) estimated_sources : np.ndarray, shape=(nsrc, nsampl) matrix containing estimated sources (must have same shape as reference_sources) compute_permutation : bool, optional compute permutation of estimate/source combinations (True by default) Returns ------- sdr : np.ndarray, shape=(nsrc,) vector of Signal to Distortion Ratios (SDR) sir : np.ndarray, shape=(nsrc,) vector of Source to Interference Ratios (SIR) sar : np.ndarray, shape=(nsrc,) vector of Sources to Artifacts Ratios (SAR) perm : np.ndarray, shape=(nsrc,) vector containing the best ordering of estimated sources in the mean SIR sense (estimated source number ``perm[j]`` corresponds to true source number ``j``). Note: ``perm`` will be ``[0, 1, ..., nsrc-1]`` if ``compute_permutation`` is ``False``. References ---------- .. [#] Emmanuel Vincent, Shoko Araki, Fabian J. Theis, Guido Nolte, Pau Bofill, Hiroshi Sawada, Alexey Ozerov, B. Vikrham Gowreesunker, Dominik Lutter and Ngoc Q.K. Duong, "The Signal Separation Evaluation Campaign (2007-2010): Achievements and remaining challenges", Signal Processing, 92, pp. 1928-1936, 2012.

### Goal
Quantifies the quality of source separation by decomposing estimated signals into components of true source, interference, and artifacts, allowing for time-invariant filter distortion.

### Parameters
- `reference_sources`: `np.ndarray` of shape `(nsrc, nsampl)` containing the ground-truth source signals.
- `estimated_sources`: `np.ndarray` of shape `(nsrc, nsampl)` containing the estimated source signals.
- `compute_permutation`, default `True`: Boolean flag to enable optimal matching of estimated sources to reference sources based on mean SIR.

### Input
- **Data:** Requires two NumPy arrays of identical shape `(nsrc, nsampl)`.
- **Preconditions:** Both arrays must contain valid audio samples. The `separation` module is currently marked as deprecated in `mir_eval`.

### Output
Returns a tuple `(sdr, sir, sar, perm)`:
- `sdr`: `np.ndarray` of shape `(nsrc,)` containing Signal to Distortion Ratios.
- `sir`: `np.ndarray` of shape `(nsrc,)` containing Source to Interference Ratios.
- `sar`: `np.ndarray` of shape `(nsrc,)` containing Sources to Artifacts Ratios.
- `perm`: `np.ndarray` of shape `(nsrc,)` containing the optimal permutation index mapping.

### Valid Call Patterns
```python
import mir_eval
# Standard usage
(sdr, sir, sar, perm) = mir_eval.separation.bss_eval_sources(reference_sources, estimated_sources)
```

### LLM Instruction Prompt
- Always ensure `reference_sources` and `estimated_sources` have the exact same shape `(nsrc, nsampl)`.
- Note that this function is part of the deprecated `separation` module.
- If performance is a concern and permutation matching is not required, set `compute_permutation=False`.

### Prompt Snippet
```text
(sdr, sir, sar, perm) = mir_eval.separation.bss_eval_sources(ref, est)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays with different `nsrc` or `nsampl` dimensions will raise a `ValueError`.
- **Silent Inputs:** Providing silent or empty arrays may trigger `ValueError` or `UserWarning` depending on the input state.
- **Deprecation:** Users should be aware that the `separation` module is deprecated and may be removed in future versions.

### Fix Code Hint
```python
# Ensure shapes match before calling
if reference_sources.shape != estimated_sources.shape:
    raise ValueError("Reference and estimated sources must have the same shape.")
```

## API Test: `bss_eval_sources_framewise`

### Signature
```python
def bss_eval_sources_framewise(reference_sources, estimated_sources, window=30 * 44100, hop=15 * 44100, compute_permutation=False)
```
_Source: source/mir_eval/separation.py:260_

_Source doc:_ Framewise computation of bss_eval_sources Please be aware that this function does not compute permutations (by default) on the possible relations between reference_sources and estimated_sources due to the dangers of a changing permutation. Therefore (by default), it assumes that ``reference_sources[i]`` corresponds to ``estimated_sources[i]``. To enable computing permutations please set ``compute_permutation`` to be ``True`` and check that the returned ``perm`` is identical for all windows. NOTE: if ``reference_sources`` and ``estimated_sources`` would be evaluated using only a single window or are shorter than the window length, the result of :func:`mir_eval.separation.bss_eval_sources` called on ``reference_sources`` and ``estimated_sources`` (with the ``compute_permutation`` parameter passed to :func:`mir_eval.separation.bss_eval_sources`) is returned. Examples -------- >>> # reference_sources[n] should be an ndarray of samples of the >>> # n'th reference source >>> # estimated_sources[n] should be the same for the n'th estimated >>> # source >>> (sdr, sir, sar, ...  perm) = mir_eval.separation.bss_eval_sources_framewise( reference_sources, ...      estimated_sources) Parameters ---------- reference_sources : np.ndarray, shape=(nsrc, nsampl) matrix containing true sources (must have the same shape as ``estimated_sources``) estimated_sources : np.ndarray, shape=(nsrc, nsampl) matrix containing estimated sources (must have the same shape as ``reference_sources``) window : int, optional Window length for framewise evaluation (default value is 30s at a sample rate of 44.1kHz) hop : int, optional Hop size for framewise evaluation (default value is 15s at a sample rate of 44.1kHz) compute_permutation : bool, optional compute permutation of estimate/source combinations for all windows (False by default) Returns ------- sdr : np.ndarray, shape=(nsrc, nframes) vector of Signal to Distortion Ratios (SDR) sir : np.ndarray, shape=(nsrc, nframes) vector of Source to Interference Ratios (SIR) sar : np.ndarray, shape=(nsrc, nframes) vector of Sources to Artifacts Ratios (SAR) perm : np.ndarray, shape=(nsrc, nframes) vector containing the best ordering of estimated sources in the mean SIR sense (estimated source number ``perm[j]`` corresponds to true source number ``j``).  Note: ``perm`` will be ``range(nsrc)`` for all windows if ``compute_permutation`` is ``False``

### Goal
Computes BSS evaluation metrics (SDR, SIR, SAR) over sliding temporal windows to analyze the time-varying performance of source separation algorithms.

### Parameters
- `reference_sources`: A `np.ndarray` of shape `(nsrc, nsampl)` containing the ground truth audio signals.
- `estimated_sources`: A `np.ndarray` of shape `(nsrc, nsampl)` containing the separated audio signals to be evaluated.
- `window`, default `30 * 44100`: Integer representing the number of samples per analysis window.
- `hop`, default `15 * 44100`: Integer representing the number of samples to shift between consecutive windows.
- `compute_permutation`, default `False`: Boolean flag; if `True`, calculates the optimal source-to-estimate permutation for each window.

### Input
- **Data:** Requires two `np.ndarray` objects of identical shape `(nsrc, nsampl)`.
- **Preconditions:** Inputs must not be empty. If inputs are shorter than the `window` length, the function defaults to standard `bss_eval_sources` behavior.

### Output
Returns a tuple of four `np.ndarray` objects: `(sdr, sir, sar, perm)`. Each array has shape `(nsrc, nframes)`, where `nframes` is the number of windows processed.

### Valid Call Patterns
```python
import mir_eval
# Standard usage with default window/hop
sdr, sir, sar, perm = mir_eval.separation.bss_eval_sources_framewise(ref, est)

# Usage with custom windowing and permutation enabled
sdr, sir, sar, perm = mir_eval.separation.bss_eval_sources_framewise(
    ref, est, window=44100, hop=22050, compute_permutation=True
)
```

### LLM Instruction Prompt
- Always use the `mir_eval.separation` namespace.
- Ensure `reference_sources` and `estimated_sources` have identical dimensions before calling.
- If `compute_permutation` is `True`, warn the user to verify that the returned `perm` is consistent across windows.

### Prompt Snippet
```text
mir_eval.separation.bss_eval_sources_framewise(reference_sources, estimated_sources, window=..., hop=..., compute_permutation=...)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays with different `nsrc` or `nsampl` dimensions will raise a `ValueError`.
- **Empty Input:** Passing empty arrays will trigger a `UserWarning` and return empty arrays.
- **Silent Sources:** Providing silent sources in the input may cause numerical instability or `ValueError` during metric calculation.

### Fix Code Hint
```python
# Ensure inputs are valid before evaluation
if reference_sources.shape != estimated_sources.shape:
    raise ValueError("Source arrays must have identical shapes.")
```

## API Test: `cemgil`

### Signature
```python
def cemgil(reference_beats, estimated_beats, cemgil_sigma=0.04)
```
_Source: source/mir_eval/beat.py:176_

_Source doc:_ Cemgil's score, computes a gaussian error of each estimated beat. Compares against the original beat times and all metrical variations. Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> cemgil_score, cemgil_max = mir_eval.beat.cemgil(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray query beat times, in seconds cemgil_sigma : float Sigma parameter of gaussian error windows (Default value = 0.04) Returns ------- cemgil_score : float Cemgil's score for the original reference beats cemgil_max : float The best Cemgil score for all metrical variations

### Goal
Computes the Cemgil score for beat tracking evaluation by calculating the Gaussian error of estimated beats relative to reference beats, accounting for metrical variations.

### Parameters
- `reference_beats`: `np.ndarray` containing reference beat times in seconds.
- `estimated_beats`: `np.ndarray` containing query beat times in seconds.
- `cemgil_sigma`, default `0.04`: `float` representing the sigma parameter of the Gaussian error windows.

### Input
- **Data:** Expects 1D NumPy arrays of beat times (in seconds).
- **Preconditions:** Inputs should be preprocessed (e.g., using `mir_eval.beat.trim_beats`) to ensure alignment and remove irrelevant segments before evaluation.
- **Formats:** Typically loaded via `mir_eval.io.load_events`.

### Output
Returns a tuple of two floats:
- `cemgil_score`: The Cemgil score for the original reference beats.
- `cemgil_max`: The best Cemgil score found across all metrical variations.

### Valid Call Patterns
```python
import mir_eval
# Example inferred from source documentation
reference_beats = mir_eval.io.load_events('reference.txt')
estimated_beats = mir_eval.io.load_events('estimated.txt')
cemgil_score, cemgil_max = mir_eval.beat.cemgil(reference_beats, estimated_beats)
```

### LLM Instruction Prompt
- Always use `mir_eval.beat.cemgil` as the function path.
- Ensure `reference_beats` and `estimated_beats` are passed as NumPy arrays.
- If the user does not specify `cemgil_sigma`, omit it to use the default value of `0.04`.
- Always capture the return as a tuple of two variables: `(cemgil_score, cemgil_max)`.

### Prompt Snippet
```text
cemgil_score, cemgil_max = mir_eval.beat.cemgil(reference_beats, estimated_beats)
```

### Common Failure Modes
- **Input Type Mismatch:** Passing lists instead of `np.ndarray` may cause errors in internal calculations.
- **Unprocessed Data:** Failing to trim beats (e.g., using `mir_eval.beat.trim_beats`) can lead to misleading scores if the reference and estimated files have different start/end offsets.
- **Empty Arrays:** Providing empty arrays for either input will result in invalid metric calculations.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays and trimmed
ref = np.array(reference_beats)
est = np.array(estimated_beats)
ref = mir_eval.beat.trim_beats(ref)
est = mir_eval.beat.trim_beats(est)

score, max_score = mir_eval.beat.cemgil(ref, est)
```

## API Test: `chords`

### Signature
```python
def chords(chord_labels, intervals, fs, **kwargs)
def chords(intervals, labels, base=None, height=None, text=False, text_kw=None, ax=None, cmap='fifths', pattern=True, **kwargs)
```
_Source: source/mir_eval/display.py:1160  (+1 more definition site/overload)_

_Source doc:_ Plot a chord annotation as a set of disjoint rectangles with semantically meaningful colors. Parameters ---------- intervals : np.ndarray, shape=(n, 2) segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. labels : list, shape=(n,) reference chord labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. base : number The vertical position of the base of the rectangles. By default, this will be the bottom of the plot. height : number The height of the rectangles. By default, this will be the top of the plot (minus ``base``). .. note:: If either `base` or `height` are provided, both must be provided. text : bool If true, each segment's label is displayed in its upper-left corner text_kw : dict If ``text == True``, the properties of the text object can be specified here. See ``matplotlib.pyplot.Text`` for valid parameters ax : matplotlib.pyplot.axes An axis handle on which to draw the segmentation. If none is provided, a new set of axes is created. cmap : {'pitch', 'fifths'} The color map to use for the chord display. If 'pitch', the colors will cycle chromatically (C, C#, D, ...) If 'fifths', the colors will cycle through the circle of fifths (C, G, D, ...). In both cases, major qualities (identified by a natural third) and suspended chords (sus2, sus4) will receive a bright color, and minor qualities (identified by a flat third) will receive a darker color. Other Qualities which are neither major nor minor will receive a light color. No-chord ('N') or out-of-gamut ('X') symbols will be colored gray. pattern : bool If `True`, segments will be filled with a hatch pattern corresponding to the chord quality as described below: - Major: no hatch - Minor: no hatch - Suspended 2nd: horizontal hatch - Suspended 4th: vertical hatch - Augmented: cross hatch - Diminished: dot hatch - Major 7th: forward slash hatch - Minor Major 7th: forward slash hatch - Dominant 7th: backward slash hatch - Minor 7th: backward slash hatch - Half-diminished 7th: dot and forward slash hatch - Diminished 7th: dot and backward slash hatch - Major 6th: cross hatch - Minor 6th: cross hatch **kwargs Additional keyword arguments to pass to ``matplotlib.axes.Axes.axvspan``. Returns -------

### Goal
Visualizes chord annotations as a sequence of colored, hatched rectangles on a Matplotlib axis to facilitate qualitative inspection of chord estimation results.

### Parameters
- `intervals`: `np.ndarray` of shape `(n, 2)` representing the start and end times of each chord segment.
- `labels`: `list` of `n` strings representing the chord labels (e.g., "C:maj").
- `base`, default `None`: Vertical starting position of the rectangles; if provided, `height` must also be provided.
- `height`, default `None`: Vertical height of the rectangles; if provided, `base` must also be provided.
- `text`, default `False`: If `True`, renders the chord label text inside the corresponding rectangle.
- `text_kw`, default `None`: Dictionary of keyword arguments passed to `matplotlib.pyplot.Text`.
- `ax`, default `None`: The Matplotlib axes object to draw on; creates a new one if `None`.
- `cmap`, default `'fifths'`: Color mapping strategy; `'pitch'` cycles chromatically, `'fifths'` cycles through the circle of fifths.
- `pattern`, default `True`: If `True`, applies specific hatch patterns to rectangles based on chord quality.
- `**kwargs`: Additional keyword arguments passed to `matplotlib.axes.Axes.axvspan`.

### Input
Requires chord intervals and labels, typically loaded via `mir_eval.io.load_labeled_intervals`. The input must be a NumPy array for intervals and a list of strings for labels.

### Output
Returns `None` (the function modifies the provided or created Matplotlib axis in-place).

### Valid Call Patterns
```python
# Standard usage with default axes
mir_eval.display.chords(intervals, labels, cmap="fifths", pattern=True)

# Usage with specific axes and text labels
mir_eval.display.chords(intervals, labels, ax=ax_handle, text=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.display.chords` for visualization.
- Ensure `intervals` and `labels` are aligned in length.
- If providing `base`, you must provide `height`.
- Use `cmap='fifths'` for circle-of-fifths coloring or `cmap='pitch'` for chromatic coloring.

### Prompt Snippet
```text
mir_eval.display.chords(intervals, labels, ax=ax, cmap='fifths', pattern=True)
```

### Common Failure Modes
- Providing `base` without `height` (or vice versa), which triggers an error.
- Mismatched lengths between `intervals` and `labels`.
- Passing invalid `cmap` strings (only `'pitch'` and `'fifths'` are supported).
- Attempting to use the function in a non-headless environment without a valid Matplotlib backend.

### Fix Code Hint
```python
# Ensure intervals and labels are correctly paired
import mir_eval
# Correct: both base and height provided
mir_eval.display.chords(intervals, labels, base=0, height=1, ax=ax)
```

## API Test: `chroma`

### Signature
```python
def chroma(chromagram, times, fs, **kwargs)
```
_Source: source/mir_eval/sonify.py:320_

_Source doc:_ Reverse synthesis of a chromagram (semitone matrix) Parameters ---------- chromagram : np.ndarray, shape=(12, times.shape[0]) Chromagram matrix, where each row represents a semitone [C->Bb] i.e., ``chromagram[3, j]`` is the magnitude of D# from ``times[j]`` to ``times[j + 1]`` times : np.ndarray, shape=(len(chord_labels),) or (len(chord_labels), 2) Either the start time of each column in the chromagram, or the time interval corresponding to each column. fs : int Sampling rate to synthesize audio data at **kwargs Additional keyword arguments to pass to :func:`mir_eval.sonify.time_frequency` Returns ------- output : np.ndarray Synthesized chromagram

### Goal
Synthesizes an audio signal from a chromagram (semitone matrix) to allow for auditory verification of pitch-class content.

### Parameters
- `chromagram`: `np.ndarray`, shape=(12, N). The chromagram matrix where each row corresponds to a semitone (C through Bb) and each column represents a time frame.
- `times`: `np.ndarray`, shape=(N,) or (N, 2). The start times of each column or the time intervals corresponding to each column in the chromagram.
- `fs`: `int`. The sampling rate (Hz) at which the output audio signal should be synthesized.
- `**kwargs`: Additional keyword arguments passed to `mir_eval.sonify.time_frequency`, such as `length` to specify the total duration of the output signal in samples.

### Input
The function expects a NumPy array for the chromagram and time indices. Precondition: The number of columns in `chromagram` must match the length of the `times` array.

### Output
Returns `output` : `np.ndarray`. A 1D NumPy array representing the synthesized audio signal at the specified sampling rate.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Basic synthesis
signal = mir_eval.sonify.chroma(chromagram, times, fs)

# Synthesis with explicit output length
signal = mir_eval.sonify.chroma(chromagram, times, fs, length=fs * 11)
```

### LLM Instruction Prompt
- When calling `mir_eval.sonify.chroma`, ensure the `chromagram` input is a 2D NumPy array with exactly 12 rows.
- Ensure the `times` array length is consistent with the number of columns in the `chromagram`.
- Use the `length` keyword argument if you need to force the output signal to a specific sample count (e.g., to match a reference audio file).

### Prompt Snippet
```text
mir_eval.sonify.chroma(chromagram, times, fs, **kwargs)
```

### Common Failure Modes
- **Shape Mismatch:** Providing a `chromagram` that does not have 12 rows will result in synthesis errors or incorrect pitch mapping.
- **Time/Column Mismatch:** If `times.shape[0]` does not equal `chromagram.shape[1]`, the function will raise an index or shape error.
- **Sampling Rate:** Providing an invalid or non-integer `fs` may cause issues in the underlying `time_frequency` synthesis.

### Fix Code Hint
```python
# Ensure chromagram is (12, N) and times is (N,)
chromagram = chromagram.reshape(12, -1)
times = np.linspace(0, duration, chromagram.shape[1])
signal = mir_eval.sonify.chroma(chromagram, times, fs)
```

## API Test: `clicks`

### Signature
```python
def clicks(times, fs, click=None, length=None)
```
_Source: source/mir_eval/sonify.py:15_

_Source doc:_ Return a signal with the signal 'click' placed at each specified time Parameters ---------- times : np.ndarray times to place clicks, in seconds fs : int desired sampling rate of the output signal click : np.ndarray click signal, defaults to a 1 kHz blip length : int desired number of samples in the output signal, defaults to ``times.max()*fs + click.shape[0] + 1`` Returns ------- click_signal : np.ndarray Synthesized click signal

### Goal
Synthesize an audio signal by placing a specific click sound at designated time intervals to facilitate auditory verification of MIR annotations.

### Parameters
- `times`: `np.ndarray` of timestamps (in seconds) where the click signal should be placed.
- `fs`: `int` representing the desired sampling rate of the output audio signal.
- `click`, default `None`: `np.ndarray` containing the audio signal to be used as the click; defaults to a 1 kHz blip if not provided.
- `length`, default `None`: `int` specifying the total number of samples in the output signal; defaults to `times.max()*fs + click.shape[0] + 1`.

### Input
The function expects `times` as a NumPy array of floats representing event times in seconds. The `fs` parameter must be an integer. If a custom `click` signal is provided, it must be a NumPy array. No specific file format is required as input, as the function operates on in-memory NumPy arrays.

### Output
Returns `click_signal` — a `np.ndarray` containing the synthesized audio signal with clicks placed at the specified times.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Basic usage
click_signal = mir_eval.sonify.clicks(times, fs)

# Specifying output length
click_signal = mir_eval.sonify.clicks(times, fs, length=1000)

# Using a custom click signal
click_signal = mir_eval.sonify.clicks(times, fs, click=np.zeros(1000))
```

### LLM Instruction Prompt
- Always ensure `times` is a `np.ndarray`.
- Ensure `fs` is an integer.
- When providing a custom `click` signal, ensure it is a `np.ndarray`.
- If `length` is provided, ensure it is an integer representing the total sample count.

### Prompt Snippet
```text
mir_eval.sonify.clicks(times, fs, click=None, length=None)
```

### Common Failure Modes
- **Type Mismatch:** Passing a list instead of a `np.ndarray` for `times` may cause indexing errors.
- **Sampling Rate Mismatch:** Providing an `fs` that does not match the intended playback environment or the `click` signal's own sampling rate can lead to pitch shifting or timing artifacts.
- **Memory Errors:** Requesting an extremely large `length` relative to the available system memory will raise a `MemoryError`.
- **Empty Input:** If `times` is empty, the function may return a signal based on the default length calculation, which might result in an unexpectedly short or empty array.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays
times = np.array([0.5, 1.0, 1.5])
fs = 44100

# Generate clicks
signal = mir_eval.sonify.clicks(times, fs)
```

## API Test: `compute_accuracy`

### Signature
```python
def compute_accuracy(true_positives, n_ref, n_est)
```
_Source: source/mir_eval/multipitch.py:248_

_Source doc:_ Compute accuracy metrics. Parameters ---------- true_positives : np.ndarray Array containing the number of true positives at each time point. n_ref : np.ndarray Array containing the number of reference frequencies at each time point. n_est : np.ndarray Array containing the number of estimate frequencies at each time point. Returns ------- precision : float ``sum(true_positives)/sum(n_est)`` recall : float ``sum(true_positives)/sum(n_ref)`` acc : float ``sum(true_positives)/sum(n_est + n_ref - true_positives)``

### Goal
Computes standard precision, recall, and accuracy metrics for multi-pitch estimation by comparing the number of correctly identified frequencies against the total reference and estimated frequencies across time points.

### Parameters
- `true_positives`: `np.ndarray` containing the count of correctly identified frequencies (true positives) at each time point.
- `n_ref`: `np.ndarray` containing the count of ground-truth reference frequencies at each time point.
- `n_est`: `np.ndarray` containing the count of estimated frequencies at each time point.

### Input
The inputs must be `np.ndarray` objects of equal length, representing counts of frequencies per time point. These arrays are typically derived from the comparison of ground-truth and estimated pitch contours or event lists.

### Output
Returns a tuple of three floats:
- `precision`: `sum(true_positives) / sum(n_est)`
- `recall`: `sum(true_positives) / sum(n_ref)`
- `acc`: `sum(true_positives) / sum(n_est + n_ref - true_positives)`

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example based on test_multipitch.py
true_positives = np.array([1, 0, 0, 3, 2])
n_ref = np.array([2, 0, 1, 3, 2])
n_est = np.array([1, 0, 2, 3, 2])

precision, recall, accuracy = mir_eval.multipitch.compute_accuracy(true_positives, n_ref, n_est)
```

### LLM Instruction Prompt
- When calling `compute_accuracy`, ensure all three input arrays are NumPy arrays of identical length.
- The function returns a tuple of three values; ensure the calling code unpacks these into `precision`, `recall`, and `accuracy` variables.
- Note that this function is located within the `mir_eval.multipitch` submodule.

### Prompt Snippet
```text
# Calculate multipitch accuracy metrics
precision, recall, acc = mir_eval.multipitch.compute_accuracy(tp_array, ref_array, est_array)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays of different lengths will result in a `ValueError` or broadcasting errors during summation.
- **Division by Zero:** If the sum of `n_est` or `n_ref` is zero, the resulting precision or recall will be `NaN` or `inf`.
- **Incorrect Module:** Attempting to call `mir_eval.compute_accuracy` directly will fail; it must be accessed via `mir_eval.multipitch.compute_accuracy`.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays and have the same length
tp = np.asarray(true_positives)
ref = np.asarray(n_ref)
est = np.asarray(n_est)

if not (tp.shape == ref.shape == est.shape):
    raise ValueError("All input arrays must have the same shape.")

precision, recall, acc = mir_eval.multipitch.compute_accuracy(tp, ref, est)
```

## API Test: `compute_err_score`

### Signature
```python
def compute_err_score(true_positives, n_ref, n_est)
```
_Source: source/mir_eval/multipitch.py:296_

_Source doc:_ Compute error score metrics. Parameters ---------- true_positives : np.ndarray Array containing the number of true positives at each time point. n_ref : np.ndarray Array containing the number of reference frequencies at each time point. n_est : np.ndarray Array containing the number of estimate frequencies at each time point. Returns ------- e_sub : float Substitution error e_miss : float Miss error e_fa : float False alarm error e_tot : float Total error

### Goal
Computes frame-level error metrics (substitution, miss, false alarm, and total error) for multipitch estimation tasks by comparing the number of true positives against reference and estimated frequency counts.

### Parameters
- `true_positives`: `np.ndarray` containing the count of correctly identified pitches at each time point.
- `n_ref`: `np.ndarray` containing the total count of reference pitches present at each time point.
- `n_est`: `np.ndarray` containing the total count of estimated pitches at each time point.

### Input
- **Data Types:** All inputs must be `np.ndarray` objects of equal length, representing counts at corresponding time points.
- **Preconditions:** The arrays must be pre-aligned in time. The values in `true_positives` must not exceed the values in `n_ref` or `n_est` at any index.

### Output
Returns a tuple of four floats:
- `e_sub`: Substitution error.
- `e_miss`: Miss error.
- `e_fa`: False alarm error.
- `e_tot`: Total error.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example based on test_multipitch.py
true_positives = np.array([1, 0, 0, 3, 2])
n_ref = np.array([2, 0, 1, 3, 2])
n_est = np.array([1, 0, 2, 3, 2])

e_sub, e_miss, e_fa, e_tot = mir_eval.multipitch.compute_err_score(
    true_positives, n_ref, n_est
)
```

### LLM Instruction Prompt
- When calling `mir_eval.multipitch.compute_err_score`, ensure all three inputs are NumPy arrays of identical length.
- Always unpack the return value into four variables: `e_sub`, `e_miss`, `e_fa`, and `e_tot`.
- Ensure the inputs represent counts per time frame, as this function is specific to the `multipitch` submodule.

### Prompt Snippet
```text
# Calculate multipitch error scores
e_sub, e_miss, e_fa, e_tot = mir_eval.multipitch.compute_err_score(tp, ref, est)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays of different lengths will raise a `ValueError` or cause broadcasting errors during internal arithmetic.
- **Invalid Counts:** If `true_positives` contains values greater than `n_ref` or `n_est`, the resulting error metrics may be mathematically invalid (e.g., negative error rates).
- **Non-NumPy Inputs:** Passing lists instead of `np.ndarray` may cause failures in element-wise arithmetic operations within the function.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays and aligned
true_positives = np.asarray(true_positives)
n_ref = np.asarray(n_ref)
n_est = np.asarray(n_est)

if not (true_positives.shape == n_ref.shape == n_est.shape):
    raise ValueError("All input arrays must have the same shape.")
```

## API Test: `compute_num_freqs`

### Signature
```python
def compute_num_freqs(frequencies)
```
_Source: source/mir_eval/multipitch.py:190_

_Source doc:_ Compute the number of frequencies for each time point. Parameters ---------- frequencies : list of np.ndarray Frequency values Returns ------- num_freqs : np.ndarray Number of frequencies at each time point.

### Goal
Calculates the count of detected frequency components present at each discrete time point within a multipitch estimation sequence.

### Parameters
- `frequencies`: A list of `np.ndarray` objects, where each array contains the frequency values detected at a specific time point.

### Input
The input must be a list of NumPy arrays. Each element in the list corresponds to a time point, and the array at that index contains the frequency values (in Hz) identified at that time. Empty arrays should be used to represent time points where no frequencies were detected.

### Output
Returns a `np.ndarray` of integers representing the total count of frequencies detected at each corresponding time point in the input list.

### Valid Call Patterns
```python
import numpy as np
import mir_eval

# Example based on test suite usage
frequencies = [
    np.array([256.0]),
    np.array([]),
    np.array([362.03867196751236, 128.0, 512.0]),
    np.array([300.0, 512.0]),
]
num_freqs = mir_eval.multipitch.compute_num_freqs(frequencies)
# Result: np.array([1, 0, 3, 2])
```

### LLM Instruction Prompt
- When calling `mir_eval.multipitch.compute_num_freqs`, ensure the input is a list of NumPy arrays.
- The function is part of the `multipitch` submodule; ensure the import path `mir_eval.multipitch` is used.
- The output is a 1D NumPy array of counts corresponding to the input list length.

### Prompt Snippet
```text
# Calculate frequency counts for multipitch evaluation
num_freqs = mir_eval.multipitch.compute_num_freqs(frequencies)
```

### Common Failure Modes
- **Type Mismatch:** Passing a single `np.ndarray` instead of a `list` of `np.ndarray` objects will cause an error, as the function expects an iterable of arrays.
- **Non-Array Elements:** Including non-array types (e.g., lists or scalars) within the `frequencies` list will result in attribute errors when the function attempts to access array properties.
- **Empty Input:** Passing an empty list will return an empty `np.ndarray`, which may cause downstream indexing errors if the calling code expects a specific length.

### Fix Code Hint
```python
# Ensure input is a list of arrays, even if empty
if not isinstance(frequencies, list):
    frequencies = [np.array(f) for f in frequencies]
num_freqs = mir_eval.multipitch.compute_num_freqs(frequencies)
```

## API Test: `compute_num_true_positives`

### Signature
```python
def compute_num_true_positives(ref_freqs, est_freqs, window=0.5, chroma=False)
```
_Source: source/mir_eval/multipitch.py:206_

_Source doc:_ Compute the number of true positives in an estimate given a reference. A frequency is correct if it is within a quartertone of the correct frequency. Parameters ---------- ref_freqs : list of np.ndarray reference frequencies (MIDI) est_freqs : list of np.ndarray estimated frequencies (MIDI) window : float Window size, in semitones chroma : bool If True, computes distances modulo n. If True, ``ref_freqs`` and ``est_freqs`` should be wrapped modulo n. Returns ------- true_positives : np.ndarray Array the same length as ref_freqs containing the number of true positives.

### Goal
Calculates the count of correctly identified pitch frequencies by comparing estimated MIDI frequencies against a reference set within a specified semitone tolerance.

### Parameters
- `ref_freqs`: A list of `np.ndarray` objects containing reference pitch frequencies in MIDI format.
- `est_freqs`: A list of `np.ndarray` objects containing estimated pitch frequencies in MIDI format.
- `window`, default `0.5`: The tolerance window size in semitones used to determine if a frequency match is considered a true positive.
- `chroma`, default `False`: If `True`, computes distances modulo $n$ (typically 12 for semitones); requires input arrays to be pre-wrapped.

### Input
The function expects two lists of equal length, where each element is a `np.ndarray` of frequencies. Inputs must be in MIDI note numbers. If `chroma=True`, the caller must ensure that all frequency values in `ref_freqs` and `est_freqs` are pre-wrapped to the appropriate modulo range.

### Output
Returns `np.ndarray`: An array of the same length as `ref_freqs`, where each element represents the count of true positive frequency matches for the corresponding frame.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Standard usage
actual = mir_eval.multipitch.compute_num_true_positives(ref_freqs, est_freqs)

# Chroma-based usage
actual = mir_eval.multipitch.compute_num_true_positives(
    ref_freqs_chroma, est_freqs_chroma, chroma=True
)
```

### LLM Instruction Prompt
- When using `compute_num_true_positives`, ensure both `ref_freqs` and `est_freqs` are provided as lists of `np.ndarray`.
- If `chroma` is set to `True`, you must manually wrap the input frequencies to the modulo range before passing them to the function.
- The output is a `np.ndarray` of integers representing the count of matches per frame.

### Prompt Snippet
```text
mir_eval.multipitch.compute_num_true_positives(ref_freqs, est_freqs, window=0.5, chroma=False)
```

### Common Failure Modes
- **Mismatched Input Lengths:** Providing `ref_freqs` and `est_freqs` lists of different lengths will cause an error during iteration.
- **Unwrapped Chroma Data:** Setting `chroma=True` without pre-wrapping the MIDI values will result in incorrect distance calculations.
- **Incorrect Units:** Passing frequencies in Hz instead of MIDI note numbers will lead to invalid true positive counts, as the window is defined in semitones.

### Fix Code Hint
```python
# Ensure inputs are lists of arrays and handle chroma wrapping if necessary
import numpy as np
ref_freqs = [np.array([60.0]), np.array([62.0])]
est_freqs = [np.array([60.1]), np.array([65.0])]
# If chroma=True, ensure values are wrapped:
# ref_freqs = [f % 12 for f in ref_freqs]
tp = mir_eval.multipitch.compute_num_true_positives(ref_freqs, est_freqs)
```

## API Test: `constant_hop_timebase`

### Signature
```python
def constant_hop_timebase(hop, end_time)
```
_Source: source/mir_eval/melody.py:195_

_Source doc:_ Generate a time series from 0 to ``end_time`` with times spaced ``hop`` apart Parameters ---------- hop : float Spacing of samples in the time series end_time : float Time series will span ``[0, end_time]`` Returns ------- times : np.ndarray Generated timebase

### Goal
Generates a deterministic, linearly spaced time series array starting at 0 and ending at `end_time` for use in resampling or aligning audio-related features.

### Parameters
- `hop`: `float`. The temporal spacing (step size) between consecutive samples in the generated time series.
- `end_time`: `float`. The upper bound of the time series; the generated series will span the interval `[0, end_time]`.

### Input
The function accepts two scalar `float` values. No specific file format is required as this is a utility function for generating in-memory NumPy arrays. It is typically used to create a common timebase for resampling melody contours or other time-series data.

### Output
Returns `np.ndarray` — A one-dimensional array of time values starting at 0, incrementing by `hop`, and ending at the largest value less than or equal to `end_time`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Generate a timebase with 0.1s spacing up to 0.35s
times = mir_eval.melody.constant_hop_timebase(0.1, 0.35)
# Result: np.array([0.0, 0.1, 0.2, 0.3])
```

### LLM Instruction Prompt
- When generating a timebase for MIR evaluation tasks, use `mir_eval.melody.constant_hop_timebase` to ensure consistency with the library's internal resampling logic.
- Ensure `hop` is a positive float.
- The output is a NumPy array; ensure downstream operations are compatible with NumPy vectorization.

### Prompt Snippet
```text
# Use mir_eval.melody.constant_hop_timebase(hop, end_time) to create a time series
```

### Common Failure Modes
- **Negative or Zero Hop:** Providing a non-positive `hop` value may result in an empty array or an infinite loop depending on the implementation logic.
- **Floating Point Precision:** Because the function uses floating-point arithmetic, the final element may not exactly equal `end_time` if `end_time` is not an exact multiple of `hop`. Use `np.allclose` when comparing results in tests.
- **Module Location:** Note that this function resides in `mir_eval.melody`, not in the top-level `mir_eval` namespace.

### Fix Code Hint
```python
# Ensure the hop is positive and the end_time is greater than 0
import numpy as np
import mir_eval

hop = 0.05
end_time = 1.0
timebase = mir_eval.melody.constant_hop_timebase(hop, end_time)
```

## API Test: `continuity`

### Signature
```python
def continuity(reference_beats, estimated_beats, continuity_phase_threshold=0.175, continuity_period_threshold=0.175)
```
_Source: source/mir_eval/beat.py:409_

_Source doc:_ Get metrics based on how much of the estimated beat sequence is continually correct. Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> CMLc, CMLt, AMLc, AMLt = mir_eval.beat.continuity(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray query beat times, in seconds continuity_phase_threshold : float Allowable ratio of how far is the estimated beat can be from the reference beat (Default value = 0.175) continuity_period_threshold : float Allowable distance between the inter-beat-interval and the inter-annotation-interval (Default value = 0.175) Returns ------- CMLc : float Correct metric level, continuous accuracy CMLt : float Correct metric level, total accuracy (continuity not required) AMLc : float Any metric level, continuous accuracy AMLt : float Any metric level, total accuracy (continuity not required)

### Goal
Computes beat tracking accuracy metrics that evaluate the continuity of the estimated beat sequence relative to a reference annotation.

### Parameters
- `reference_beats`: `np.ndarray` of reference beat times in seconds.
- `estimated_beats`: `np.ndarray` of estimated beat times in seconds.
- `continuity_phase_threshold`, default `0.175`: The allowable ratio of deviation for an estimated beat relative to a reference beat.
- `continuity_period_threshold`, default `0.175`: The allowable distance between the inter-beat-interval and the inter-annotation-interval.

### Input
- **Data:** Expects 1D `np.ndarray` objects containing timestamps (floats).
- **Preconditions:** Inputs should be preprocessed (e.g., using `mir_eval.beat.trim_beats`) to ensure alignment and remove irrelevant segments. Annotations should be loaded via `mir_eval.io.load_events`.

### Output
Returns a tuple of four floats: `(CMLc, CMLt, AMLc, AMLt)`.
- `CMLc`: Correct Metric Level, continuous accuracy.
- `CMLt`: Correct Metric Level, total accuracy (continuity not required).
- `AMLc`: Any Metric Level, continuous accuracy.
- `AMLt`: Any Metric Level, total accuracy (continuity not required).

### Valid Call Patterns
```python
import mir_eval
# Standard usage
CMLc, CMLt, AMLc, AMLt = mir_eval.beat.continuity(reference_beats, estimated_beats)

# Edge case usage from test suite
result = mir_eval.beat.continuity(np.array([6.0, 6.0]), np.array([6.0, 7.0]))
```

### LLM Instruction Prompt
- Always pass `reference_beats` and `estimated_beats` as `np.ndarray` objects.
- Ensure inputs are trimmed using `mir_eval.beat.trim_beats` before calling this function to ensure reproducible evaluation.
- The function returns a 4-tuple; ensure all four variables are captured.

### Prompt Snippet
```text
CMLc, CMLt, AMLc, AMLt = mir_eval.beat.continuity(reference_beats, estimated_beats)
```

### Common Failure Modes
- **Input Type Mismatch:** Passing lists instead of `np.ndarray` may cause internal validation errors.
- **Unprocessed Data:** Failing to trim beats can lead to misleading accuracy scores if the reference and estimated sequences have different start/end offsets.
- **Empty Arrays:** Passing empty arrays or arrays with insufficient beats may result in zero-valued metrics or unexpected behavior in the continuity logic.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays and trimmed
import numpy as np
import mir_eval
ref = np.array(reference_list)
est = np.array(estimated_list)
ref = mir_eval.beat.trim_beats(ref)
est = mir_eval.beat.trim_beats(est)
CMLc, CMLt, AMLc, AMLt = mir_eval.beat.continuity(ref, est)
```

## API Test: `deprecated`

### Signature
```python
def deprecated(*, version, version_removed)
```
_Source: source/mir_eval/util.py:946_

_Source doc:_ Mark a function as deprecated. Using the decorated (old) function will result in a warning.

### Goal
The `deprecated` function is a decorator used to signal that a specific function or utility within the `mir_eval` library is slated for removal, ensuring developers are warned when using legacy code.

### Parameters
- `version`: The version of `mir_eval` in which the function was first marked as deprecated.
- `version_removed`: The version of `mir_eval` in which the function is scheduled to be removed.

### Input
The caller must provide the `version` and `version_removed` as keyword arguments. This function is intended to be used as a decorator applied to a function definition.

### Output
Returns `unspecified` — The function returns a decorator that wraps the target function to issue a warning upon invocation.

### Valid Call Patterns
```python
from mir_eval.util import deprecated

@deprecated(version='0.8.1', version_removed='0.9')
def my_old_function():
    pass
```
*(Note: This call pattern is inferred from the signature as no explicit test-suite example was provided.)*

### LLM Instruction Prompt
- When documenting or refactoring `mir_eval` code, use `@deprecated(version='...', version_removed='...')` to decorate functions that are no longer recommended for use.
- Ensure both `version` and `version_removed` are provided as keyword arguments.

### Prompt Snippet
```text
@deprecated(version='<version_added>', version_removed='<version_removed>')
```

### Common Failure Modes
- **Missing Keyword Arguments:** Passing arguments positionally will result in a `TypeError` because the signature enforces keyword-only arguments (`*`).
- **Ignoring Warnings:** Developers may overlook the runtime warnings generated by decorated functions, leading to code that will break in future library updates.
- **Incorrect Versioning:** Providing inaccurate version strings can mislead users regarding the timeline for library migration.

### Fix Code Hint
```python
# Ensure arguments are passed as keyword arguments
@deprecated(version='0.8.1', version_removed='0.9')
def function_to_deprecate():
    # Implementation
    pass
```

## API Test: `detection`

### Signature
```python
def detection(reference_tempi, reference_weight, estimated_tempi, tol=0.08)
def detection(reference_intervals, estimated_intervals, window=0.5, beta=1.0, trim=False)
```
_Source: source/mir_eval/segment.py:168  (+1 more definition site/overload)_

_Source doc:_ Boundary detection hit-rate. A hit is counted whenever an reference boundary is within ``window`` of a estimated boundary.  Note that each boundary is matched at most once: this is achieved by computing the size of a maximal matching between reference and estimated boundary points, subject to the window constraint. Examples -------- >>> ref_intervals, _ = mir_eval.io.load_labeled_intervals('ref.lab') >>> est_intervals, _ = mir_eval.io.load_labeled_intervals('est.lab') >>> # With 0.5s windowing >>> P05, R05, F05 = mir_eval.segment.detection(ref_intervals, ...                                            est_intervals, ...                                            window=0.5) >>> # With 3s windowing >>> P3, R3, F3 = mir_eval.segment.detection(ref_intervals, ...                                         est_intervals, ...                                         window=3) >>> # Ignoring hits for the beginning and end of track >>> P, R, F = mir_eval.segment.detection(ref_intervals, ...                                      est_intervals, ...                                      window=0.5, ...                                      trim=True) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. window : float > 0 size of the window of 'correctness' around ground-truth beats (in seconds) (Default value = 0.5) beta : float > 0 weighting constant for F-measure. (Default value = 1.0) trim : boolean if ``True``, the first and last boundary times are ignored. Typically, these denote start (0) and end-markers. (Default value = False) Returns ------- precision : float precision of estimated predictions recall : float recall of reference reference boundaries f_measure : float F-measure (weighted harmonic mean of ``precision`` and ``recall``)

### Goal
Computes the boundary detection hit-rate for music segmentation by calculating the precision, recall, and F-measure of estimated segment boundaries relative to ground-truth intervals.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). Reference segment intervals, typically loaded via `mir_eval.io.load_intervals` or `mir_eval.io.load_labeled_intervals`.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). Estimated segment intervals, in the same format as `reference_intervals`.
- `window`, default `0.5`: `float > 0`. The tolerance window in seconds within which an estimated boundary is considered a hit.
- `beta`, default `1.0`: `float > 0`. The weighting constant for the F-measure calculation.
- `trim`, default `False`: `boolean`. If `True`, ignores the first and last boundary times, which often represent track start/end markers.

### Input
Requires two `np.ndarray` objects containing segment intervals. Data should be pre-processed to ensure they are in the format returned by `mir_eval.io` loaders. If intervals are empty, the function will issue a `UserWarning`.

### Output
Returns a tuple of three floats: `(precision, recall, f_measure)`.

### Valid Call Patterns
```python
# Standard usage with default parameters
P, R, F = mir_eval.segment.detection(ref_intervals, est_intervals)

# Usage with custom window and trimming
P, R, F = mir_eval.segment.detection(ref_intervals, est_intervals, window=0.5, trim=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.segment.detection` as the receiver.
- Ensure `reference_intervals` and `estimated_intervals` are `np.ndarray` objects of shape `(n, 2)`.
- When evaluating segment boundaries, consider setting `trim=True` if the dataset includes explicit start (0) and end-of-track markers.

### Prompt Snippet
```text
import mir_eval
# Assuming ref_intervals and est_intervals are loaded as np.ndarray
precision, recall, f_measure = mir_eval.segment.detection(ref_intervals, est_intervals, window=0.5, trim=True)
```

### Common Failure Modes
- Passing empty arrays: The function will trigger a `UserWarning` and return `0` for all metrics.
- Mismatched array shapes: Ensure inputs are `(n, 2)` arrays; passing flat arrays or incorrect dimensions will cause internal validation errors.
- Incorrect window size: Using a `window` that is too small relative to the annotation precision may result in artificially low F-measure scores.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are valid numpy arrays before calling
ref = np.array([[0, 10], [10, 20]])
est = np.array([[0, 10.2], [10.2, 20]])
# Use trim=True if the first/last boundaries are just track markers
p, r, f = mir_eval.segment.detection(ref, est, trim=True)
```

## API Test: `deviation`

### Signature
```python
def deviation(reference_intervals, estimated_intervals, trim=False)
```
_Source: source/mir_eval/segment.py:252_

_Source doc:_ Compute the median deviations between reference and estimated boundary times. Examples -------- >>> ref_intervals, _ = mir_eval.io.load_labeled_intervals('ref.lab') >>> est_intervals, _ = mir_eval.io.load_labeled_intervals('est.lab') >>> r_to_e, e_to_r = mir_eval.boundary.deviation(ref_intervals, ...                                              est_intervals) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. trim : boolean if ``True``, the first and last intervals are ignored. Typically, these denote start (0.0) and end-of-track markers. (Default value = False) Returns ------- reference_to_estimated : float median time from each reference boundary to the closest estimated boundary estimated_to_reference : float median time from each estimated boundary to the closest reference boundary

### Goal
Computes the median temporal deviation between reference and estimated segment boundaries to evaluate the precision of structural segmentation.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). The ground-truth segment intervals.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). The predicted segment intervals.
- `trim`, default `False`: If `True`, ignores the first and last intervals, which typically represent track start (0.0) and end markers.

### Input
- **Data Format:** Requires `np.ndarray` objects containing interval pairs, typically loaded via `mir_eval.io.load_intervals` or `mir_eval.io.load_labeled_intervals`.
- **Preconditions:** Inputs must be formatted as arrays of shape (N, 2) where each row represents a segment start and end time.

### Output
Returns a tuple of two floats:
- `reference_to_estimated`: The median time from each reference boundary to the nearest estimated boundary.
- `estimated_to_reference`: The median time from each estimated boundary to the nearest reference boundary.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example from test suite
correct_intervals = np.array([[0, 1], [1, 2]])
r_to_e, e_to_r = mir_eval.segment.deviation(correct_intervals, correct_intervals)
```

### LLM Instruction Prompt
- Always use `mir_eval.segment.deviation` to access this function.
- Ensure inputs are `np.ndarray` objects of shape (N, 2).
- When evaluating structural segmentation, consider setting `trim=True` if the input intervals include global start/end markers that do not represent internal segment boundaries.

### Prompt Snippet
```text
# Compute boundary deviation
r_to_e, e_to_r = mir_eval.segment.deviation(ref_intervals, est_intervals, trim=True)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays that are not of shape (N, 2) will cause internal validation errors.
- **Empty Arrays:** Passing empty arrays may result in undefined median calculations.
- **Incorrect Module:** Note that while the docstring mentions `mir_eval.boundary`, the function is located within the `mir_eval.segment` submodule.

### Fix Code Hint
```python
# Ensure intervals are loaded correctly before passing to deviation
import mir_eval
ref_intervals = mir_eval.io.load_intervals('ref.txt')
est_intervals = mir_eval.io.load_intervals('est.txt')
r_to_e, e_to_r = mir_eval.segment.deviation(ref_intervals, est_intervals, trim=True)
```

## API Test: `directional_hamming_distance`

### Signature
```python
def directional_hamming_distance(reference_intervals, estimated_intervals)
```
_Source: source/mir_eval/chord.py:1359_

_Source doc:_ Compute the directional hamming distance between reference and estimated intervals as defined by [#harte2010towards]_ and used for MIREX 'OverSeg', 'UnderSeg' and 'MeanSeg' measures. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> overseg = 1 - mir_eval.chord.directional_hamming_distance( ...     ref_intervals, est_intervals) >>> underseg = 1 - mir_eval.chord.directional_hamming_distance( ...     est_intervals, ref_intervals) >>> seg = min(overseg, underseg) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2), dtype=float Reference chord intervals to score against. estimated_intervals : np.ndarray, shape=(m, 2), dtype=float Estimated chord intervals to score against. Returns ------- directional hamming distance : float directional hamming distance between reference intervals and estimated intervals.

### Goal
Computes the directional Hamming distance between two sets of intervals to quantify segmentation alignment, commonly used for calculating MIREX 'OverSeg', 'UnderSeg', and 'MeanSeg' metrics.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2), dtype=float. The ground-truth chord intervals to score against.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2), dtype=float. The estimated chord intervals to evaluate.

### Input
The function expects two NumPy arrays of shape `(N, 2)` containing start and end times for intervals. Intervals must be non-overlapping and chronologically ordered. Preprocessing via `mir_eval.io.load_labeled_intervals` is the standard way to obtain these arrays from annotation files.

### Output
Returns a `float` representing the directional Hamming distance between the reference and estimated intervals.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example based on test suite
ref_ivs = np.array([[0.0, 1.0], [1.0, 2.0], [2.0, 3.0]])
est_ivs = np.array([[0.0, 0.9], [0.9, 1.8], [1.8, 2.5]])

dhd = mir_eval.chord.directional_hamming_distance(ref_ivs, est_ivs)
```

### LLM Instruction Prompt
- Always use `mir_eval.chord.directional_hamming_distance` as the function path.
- Ensure both inputs are `np.ndarray` objects with shape `(n, 2)` and `dtype=float`.
- Remember that this metric is directional: `directional_hamming_distance(A, B)` is not equal to `directional_hamming_distance(B, A)`.
- Use this function to derive segmentation metrics by calculating `1 - distance`.

### Prompt Snippet
```text
# Calculate directional Hamming distance for chord segmentation
dhd_score = mir_eval.chord.directional_hamming_distance(reference_intervals, estimated_intervals)
```

### Common Failure Modes
- **Overlapping Intervals:** Passing intervals that overlap within the same set will raise a `ValueError`.
- **Incorrect Shape:** Passing arrays that are not `(n, 2)` will cause internal validation errors.
- **Directional Confusion:** Assuming the metric is symmetric; users must compute it in both directions (ref-to-est and est-to-ref) to derive comprehensive segmentation scores like 'MeanSeg'.

### Fix Code Hint
```python
# Ensure intervals are sorted and non-overlapping before calling
import mir_eval.util
ref_ivs = mir_eval.util.validate_intervals(ref_ivs)
est_ivs = mir_eval.util.validate_intervals(est_ivs)
dhd = mir_eval.chord.directional_hamming_distance(ref_ivs, est_ivs)
```

## API Test: `encode`

### Signature
```python
def encode(chord_label, reduce_extended_chords=False, strict_bass_intervals=False)
```
_Source: source/mir_eval/chord.py:471_

_Source doc:_ Translate a chord label to numerical representations for evaluation. Parameters ---------- chord_label : str Chord label to encode. reduce_extended_chords : bool Whether to map the upper voicings of extended chords (9's, 11's, 13's) to semitone extensions. (Default value = False) strict_bass_intervals : bool Whether to require that the bass scale degree is present in the chord. (Default value = False) Returns ------- root_number : int Absolute semitone of the chord's root. semitone_bitmap : np.ndarray, dtype=int 12-dim vector of relative semitones in the chord spelling. bass_number : int Relative semitone of the chord's bass note, e.g. 0=root, 7=fifth, etc.

### Goal
Translates a string-based chord label into a standardized numerical representation (root, semitone bitmap, and bass note) suitable for quantitative MIR chord evaluation.

### Parameters
- `chord_label`: A string representing the chord (e.g., "C:maj", "G:min7/b3").
- `reduce_extended_chords`, default `False`: If `True`, maps upper extensions (9ths, 11ths, 13ths) to their corresponding semitone intervals within the octave.
- `strict_bass_intervals`, default `False`: If `True`, enforces that the specified bass note must exist as a valid scale degree within the chord's spelling.

### Input
- **Data:** A string representing a chord label.
- **Preconditions:** The label must conform to the chord syntax expected by `mir_eval`. No external file loading is required; the function operates on the provided string.

### Output
Returns a tuple containing:
- `root_number` (int): The absolute semitone of the chord's root (0-11).
- `semitone_bitmap` (np.ndarray, dtype=int): A 12-dimensional binary vector where 1 indicates the presence of a semitone relative to the root.
- `bass_number` (int): The relative semitone of the bass note (e.g., 0 for root, 7 for fifth).

### Valid Call Patterns
```python
import mir_eval
# Standard encoding
root, intervals, bass = mir_eval.chord.encode("C:maj")

# Encoding with strict bass validation
root, intervals, bass = mir_eval.chord.encode(
    "G:dim(4)/6", reduce_extended_chords=False, strict_bass_intervals=True
)
```

### LLM Instruction Prompt
- Always use the `mir_eval.chord.encode` namespace.
- Ensure `chord_label` is a string.
- When evaluating chord transcription accuracy, set `strict_bass_intervals=True` if the evaluation criteria require the bass note to be a constituent part of the chord.

### Prompt Snippet
```text
mir_eval.chord.encode(chord_label, reduce_extended_chords=False, strict_bass_intervals=False)
```

### Common Failure Modes
- **Invalid Bass Notes:** Setting `strict_bass_intervals=True` will raise an error if the provided bass note is not a valid interval within the chord's spelling (e.g., a non-chord tone in the bass).
- **Malformed Labels:** Passing a string that does not follow standard MIR chord notation will result in a parsing error.

### Fix Code Hint
```python
# If strict_bass_intervals=True causes a crash, verify the chord spelling 
# or set strict_bass_intervals=False to allow non-chord bass notes.
try:
    root, intervals, bass = mir_eval.chord.encode(label, strict_bass_intervals=True)
except ValueError:
    root, intervals, bass = mir_eval.chord.encode(label, strict_bass_intervals=False)
```

## API Test: `encode_many`

### Signature
```python
def encode_many(chord_labels, reduce_extended_chords=False)
```
_Source: source/mir_eval/chord.py:523_

_Source doc:_ Translate a set of chord labels to numerical representations for sane evaluation. Parameters ---------- chord_labels : list Set of chord labels to encode. reduce_extended_chords : bool Whether to map the upper voicings of extended chords (9's, 11's, 13's) to semitone extensions. (Default value = False) Returns ------- root_number : np.ndarray, dtype=int Absolute semitone of the chord's root. interval_bitmap : np.ndarray, dtype=int 12-dim vector of relative semitones in the given chord quality. bass_number : np.ndarray, dtype=int Relative semitones of the chord's bass notes.

### Goal
Translates a collection of symbolic chord labels into standardized numerical representations (roots, interval bitmaps, and bass notes) to facilitate reproducible chord recognition evaluation.

### Parameters
- `chord_labels`: A list of strings representing chord labels (e.g., "C:maj", "B:maj(*1,*3)/5").
- `reduce_extended_chords`, default `False`: A boolean flag; if `True`, maps upper voicings of extended chords (9ths, 11ths, 13ths) to their corresponding semitone extensions.

### Input
- **Data:** A list of strings.
- **Preconditions:** Labels must follow standard MIR chord notation formats compatible with the library's internal parser. Non-chord labels (e.g., "N") are supported and will be encoded with specific numerical placeholders (e.g., -1 for roots/bass).

### Output
Returns a tuple of three `np.ndarray` objects:
1. `root_number`: An array of integers representing the absolute semitone of each chord's root.
2. `interval_bitmap`: A 2D array (N x 12) of integers representing the 12-dim vector of relative semitones for each chord quality.
3. `bass_number`: An array of integers representing the relative semitones of each chord's bass note.

### Valid Call Patterns
```python
import mir_eval
labels = ["B:maj(*1,*3)/5", "N", "C:min"]
roots, intervals, basses = mir_eval.chord.encode_many(labels)
```

### LLM Instruction Prompt
- When using `encode_many`, ensure the input is a list of strings.
- Always unpack the three returned NumPy arrays: `roots`, `intervals`, and `basses`.
- Use `reduce_extended_chords=True` only if the evaluation task requires collapsing extended chord voicings to base semitone components.

### Prompt Snippet
```text
# Example of encoding chord labels for evaluation
roots, intervals, basses = mir_eval.chord.encode_many(chord_labels, reduce_extended_chords=False)
```

### Common Failure Modes
- **Type Mismatch:** Passing a single string instead of a list of strings will cause the parser to iterate over characters rather than labels.
- **Invalid Label Format:** Providing labels that do not conform to the expected chord syntax will raise a `ValueError` during the parsing process.
- **Dimension Mismatch:** Assuming the output arrays are 1D; note that `interval_bitmap` is a 2D array (N x 12).

### Fix Code Hint
```python
# Ensure input is a list, even for a single label
roots, intervals, basses = mir_eval.chord.encode_many([single_label])
```

## API Test: `establishment_FPR`

### Signature
```python
def establishment_FPR(reference_patterns, estimated_patterns, similarity_metric='cardinality_score')
```
_Source: source/mir_eval/pattern.py:238_

_Source doc:_ Compute the establishment F1 Score, Precision and Recall. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> F, P, R = mir_eval.pattern.establishment_FPR(ref_patterns, ...                                              est_patterns) Parameters ---------- reference_patterns : list The reference patterns in the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format similarity_metric : str A string representing the metric to be used when computing the similarity matrix. Accepted values: - "cardinality_score": Count of the intersection between occurrences. (Default value = "cardinality_score") Returns ------- f_measure : float The establishment F1 Score precision : float The establishment Precision recall : float The establishment Recall

### Goal
Computes the establishment F1-measure, precision, and recall for pattern discovery evaluation by comparing reference and estimated pattern sets.

### Parameters
- `reference_patterns`: A list of reference patterns in the format returned by `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns in the same format as the reference patterns.
- `similarity_metric`, default `'cardinality_score'`: A string specifying the metric used to compute the similarity matrix. Currently, only `'cardinality_score'` (the count of the intersection between occurrences) is supported.

### Input
The function expects two lists of patterns, typically loaded via `mir_eval.io.load_patterns()`. Precondition: Both `reference_patterns` and `estimated_patterns` must be formatted according to the library's internal pattern representation requirements.

### Output
Returns a tuple of three floats:
- `f_measure`: The establishment F1 Score.
- `precision`: The establishment Precision.
- `recall`: The establishment Recall.

### Valid Call Patterns
```python
import mir_eval
# Example inferred from signature and docstring
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
f, p, r = mir_eval.pattern.establishment_FPR(ref_patterns, est_patterns)
```

### LLM Instruction Prompt
- When calling `establishment_FPR`, ensure both `reference_patterns` and `estimated_patterns` are loaded using `mir_eval.io.load_patterns()` to guarantee the correct list structure.
- The function returns a tuple of three values; always unpack them as `f_measure, precision, recall = ...`.
- Only use `'cardinality_score'` for the `similarity_metric` parameter as it is the only accepted value.

### Prompt Snippet
```text
# Calculate pattern discovery metrics
f, p, r = mir_eval.pattern.establishment_FPR(ref_patterns, est_patterns, similarity_metric='cardinality_score')
```

### Common Failure Modes
- **Format Mismatch:** Passing raw data that has not been processed by `mir_eval.io.load_patterns()` will likely result in an error or incorrect metric calculation.
- **Invalid Metric:** Providing a string other than `'cardinality_score'` for `similarity_metric` will cause the function to fail, as no other metrics are currently implemented.
- **Empty Inputs:** Passing empty lists may lead to division-by-zero errors or undefined behavior depending on the internal implementation of the F-measure calculation.

### Fix Code Hint
```python
# Ensure inputs are loaded correctly before evaluation
try:
    f, p, r = mir_eval.pattern.establishment_FPR(ref_patterns, est_patterns)
except Exception as e:
    print(f"Evaluation failed: {e}")
```

## API Test: `evaluate`

### Signature
```python
def evaluate(ref_time, ref_freq, est_time, est_freq, est_voicing=None, ref_reward=None, **kwargs)
```
_Source: source/mir_eval/melody.py_

_Source doc:_ Evaluate two melody (predominant f0) transcriptions, where the first is treated as the reference (ground truth) and the second as the estimate to be evaluated (prediction).

### Goal
Computes a comprehensive suite of melody extraction accuracy metrics by comparing a reference predominant f0 contour against an estimated contour.

### Parameters
- `ref_time`: `np.ndarray` — Time of each reference frequency value.
- `ref_freq`: `np.ndarray` — Array of reference frequency values.
- `est_time`: `np.ndarray` — Time of each estimated frequency value.
- `est_freq`: `np.ndarray` — Array of estimated frequency values.
- `est_voicing`: `np.ndarray` — Estimate voicing confidence. Default `None`, which infers voicing from `est_freq` (<= 0.0 is unvoiced, > 0.0 is voiced).
- `ref_reward`: `np.ndarray` — Reference pitch estimation reward. Default `None`, which weights all frames equally.
- `**kwargs`: Additional keyword arguments passed to underlying metric or preprocessing functions.

### Input
- **Data Types:** Expects `numpy.ndarray` objects for time and frequency series.
- **Preconditions:** Annotations should be loaded using `mir_eval.io` utilities. Data must be aligned or resampled to a common time base if necessary before evaluation.
- **Formats:** Standard MIR annotation formats for pitch contours (e.g., time-frequency pairs).

### Output
Returns `scores` — A `dict` where keys are metric names (`str`) and values are the corresponding calculated scores (`float`).

### Valid Call Patterns
```python
import mir_eval
ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt')
est_time, est_freq = mir_eval.io.load_time_series('est.txt')
scores = mir_eval.melody.evaluate(ref_time, ref_freq, est_time, est_freq)
```

### LLM Instruction Prompt
- Always use `mir_eval.melody.evaluate` for melody tasks.
- Ensure input arrays are `numpy.ndarray` types.
- If voicing information is not explicitly provided, rely on the default behavior where `est_freq <= 0.0` is treated as unvoiced.
- Always cite the project: *C. Raffel, B. McFee, E. J. Humphrey, J. Salamon, O. Nieto, D. Liang, and D. P. W. Ellis, "mir_eval: A Transparent Implementation of Common MIR Metrics", ISMIR 2014.*

### Prompt Snippet
```text
# Example of calling melody evaluation
scores = mir_eval.melody.evaluate(ref_time, ref_freq, est_time, est_freq)
```

### Common Failure Modes
- **Shape Mismatch:** Providing `ref_time` and `ref_freq` arrays of different lengths will trigger validation errors.
- **Unvoiced Handling:** Misinterpreting the default voicing logic (where `est_freq <= 0.0` is unvoiced) can lead to incorrect accuracy scores if the estimate uses a different convention (e.g., `NaN` for unvoiced).
- **Missing Preprocessing:** Failing to resample or trim inputs to match the reference time grid can result in invalid metric calculations.

### Fix Code Hint
```python
# Ensure arrays are aligned and valid before evaluation
import numpy as np
ref_time = np.array(ref_time)
ref_freq = np.array(ref_freq)
# Use mir_eval.melody.resample_melody_series if time grids differ
```

## API Test: `events`

### Signature
```python
def events(times, labels=None, base=None, height=None, ax=None, text_kw=None, prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:543_

_Source doc:_ Plot event times as a set of vertical lines Parameters ---------- times : np.ndarray, shape=(n,) event times, in the format returned by :func:`mir_eval.io.load_events` or :func:`mir_eval.io.load_labeled_events`. labels : list, shape=(n,), optional event labels, in the format returned by :func:`mir_eval.io.load_labeled_events`. base : number The vertical position of the base of the line. By default, this will be the bottom of the plot. height : number The height of the lines. By default, this will be the top of the plot (minus `base`). .. note:: If either `base` or `height` are provided, both must be provided. ax : matplotlib.pyplot.axes An axis handle on which to draw the segmentation. If none is provided, a new set of axes is created. text_kw : dict If `labels` is provided, the properties of the text objects can be specified here. See `matplotlib.pyplot.Text` for valid parameters prop_cycle : cycle.Cycler An optional property cycle object to specify style properties. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to pass to `matplotlib.pyplot.vlines`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes discrete temporal events (such as beats or onsets) as vertical lines on a Matplotlib axis to facilitate inspection of MIR annotation data.

### Parameters
- `times`: A NumPy array of shape `(n,)` containing the timestamps of the events in seconds.
- `labels`, default `None`: A list of strings of length `n` corresponding to the event labels.
- `base`, default `None`: The vertical coordinate (y-axis) where the lines begin.
- `height`, default `None`: The vertical length of the lines.
- `ax`, default `None`: A Matplotlib axis object; if `None`, a new figure and axis are created.
- `text_kw`, default `None`: A dictionary of keyword arguments passed to `matplotlib.pyplot.Text` for label styling.
- `prop_cycle`, default `None`: A `cycler.Cycler` object for defining line style properties.
- `**kwargs`: Additional keyword arguments passed directly to `matplotlib.pyplot.vlines`.

### Input
The `times` array must be a 1D NumPy array of floats, typically obtained via `mir_eval.io.load_events`. If `labels` are provided, they must match the length of the `times` array. Precondition: `base` and `height` must both be provided if either is specified.

### Output
Returns `matplotlib.pyplot.axes._subplots.AxesSubplot`: A handle to the Matplotlib axes containing the rendered event visualization.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

# Basic usage with labels
beats = mir_eval.io.load_events("data/beat/ref00.txt")
mir_eval.display.events(beats, labels=["event1", "event2"])

# Using an existing axis
fig, ax = plt.subplots()
mir_eval.display.events(beats, ax=ax, color='r', linestyle='--')
```

### LLM Instruction Prompt
- Always use `mir_eval.display.events` as the fully qualified name.
- Ensure `times` is a 1D NumPy array.
- If providing `base`, you must also provide `height`.
- Use `**kwargs` to pass styling parameters like `color` or `linestyle` directly to the underlying `vlines` call.

### Prompt Snippet
```text
mir_eval.display.events(times, labels=None, base=None, height=None, ax=None, **kwargs)
```

### Common Failure Modes
- Providing `base` without `height` (or vice versa), which triggers an error as both are required for custom vertical positioning.
- Passing a `times` array that is not 1D, which will cause `vlines` to fail.
- Mismatch between the length of `times` and `labels` when `labels` is provided.

### Fix Code Hint
```python
# Ensure base and height are paired
if base is not None or height is not None:
    if base is None or height is None:
        raise ValueError("Both 'base' and 'height' must be provided.")
```

## API Test: `f_measure`

### Signature
```python
def f_measure(reference_beats, estimated_beats, f_measure_threshold=0.07)
def f_measure(reference_onsets, estimated_onsets, window=0.05)
def f_measure(precision, recall, beta=1.0)
```
_Source: source/mir_eval/beat.py:135  (+2 more definition site/overload)_

_Source doc:_ Compute the F-measure of correct vs incorrectly predicted beats. "Correctness" is determined over a small window. Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> f_measure = mir_eval.beat.f_measure(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray estimated beat times, in seconds f_measure_threshold : float Window size, in seconds (Default value = 0.07) Returns ------- f_score : float The computed F-measure score

### Goal
Computes the F-measure score to evaluate the accuracy of beat or onset detection by comparing estimated event times against ground-truth reference times within a specified temporal window.

### Parameters
- `reference_beats`: `np.ndarray` containing reference beat times in seconds.
- `estimated_beats`: `np.ndarray` containing estimated beat times in seconds.
- `f_measure_threshold`, default `0.07`: The window size in seconds used to determine if an estimated beat is "correct" (i.e., within this distance of a reference beat).

### Input
- **Data:** Expects `np.ndarray` objects.
- **Preconditions:** Annotations should be loaded using `mir_eval.io.load_events`. It is common practice to preprocess data using task-specific utilities like `mir_eval.beat.trim_beats` before evaluation to ensure temporal alignment.
- **Validation:** The function performs internal input validation; empty input arrays will trigger a `UserWarning` and return a score of 0.

### Output
Returns `f_score` (float): The computed F-measure, representing the harmonic mean of precision and recall.

### Valid Call Patterns
```python
import mir_eval
# Standard beat evaluation pattern
reference_beats = mir_eval.io.load_events('reference.txt')
estimated_beats = mir_eval.io.load_events('estimated.txt')
f_score = mir_eval.beat.f_measure(reference_beats, estimated_beats)

# Onset evaluation pattern
f_score = mir_eval.onset.f_measure(reference_onsets, estimated_onsets)
```

### LLM Instruction Prompt
- Always use `mir_eval.beat.f_measure` or `mir_eval.onset.f_measure` depending on the task.
- Ensure inputs are `np.ndarray` objects representing time in seconds.
- When evaluating beats, consider applying `mir_eval.beat.trim_beats` to both inputs first to ensure consistent evaluation windows.

### Prompt Snippet
```text
# Example of calculating F-measure for beats
f_score = mir_eval.beat.f_measure(ref_beats, est_beats, f_measure_threshold=0.07)
```

### Common Failure Modes
- **Empty Inputs:** Passing empty arrays will result in a `UserWarning` and a return value of 0.0.
- **Mismatched Units:** Providing inputs in samples rather than seconds will lead to incorrect evaluation; ensure all inputs are converted to seconds.
- **Unsorted Arrays:** While not explicitly stated as a hard failure, ensure event arrays are sorted chronologically to maintain expected behavior in temporal matching.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are sorted and in seconds
ref = np.sort(reference_beats)
est = np.sort(estimated_beats)
score = mir_eval.beat.f_measure(ref, est)
```

## API Test: `filter_kwargs`

### Signature
```python
def filter_kwargs(_function, *args, **kwargs)
```
_Source: source/mir_eval/util.py:860_

_Source doc:_ Given a function and args and keyword args to pass to it, call the function but using only the keyword arguments which it accepts.  This is equivalent to redefining the function with an additional ``**kwargs`` to accept slop keyword args. If the target function already accepts ``**kwargs`` parameters, no filtering is performed. Parameters ---------- _function : callable Function to call.  Can take in any number of args or kwargs *args **kwargs Arguments and keyword arguments to _function.

### Goal
`filter_kwargs` enables the safe execution of MIR metric functions by automatically stripping unsupported keyword arguments, allowing for flexible parameter passing in evaluation pipelines.

### Parameters
- `_function`: A callable function to be executed.
- `*args`: Positional arguments to be passed to `_function`.
- `**kwargs`: Keyword arguments to be passed to `_function`, filtered to include only those accepted by the function signature.

### Input
The caller must provide a valid callable object as `_function`. The `**kwargs` provided should contain the parameters intended for the function; `filter_kwargs` will inspect the signature of `_function` and discard any keys not explicitly defined in its parameters, unless the function already accepts `**kwargs`.

### Output
Returns the result of the function call. The return type is determined by the return value of `_function`.

### Valid Call Patterns
```python
# Inferred from signature:
from mir_eval.util import filter_kwargs

# Example: calling a metric function with extra, potentially unsupported arguments
result = filter_kwargs(my_metric_function, reference, estimated, **all_params)
```

### LLM Instruction Prompt
- When using `filter_kwargs`, ensure the first argument is a callable function.
- Use this utility when passing a shared dictionary of configuration parameters to multiple metric functions that may have different signature requirements.
- Do not assume the return type; it depends entirely on the provided `_function`.

### Prompt Snippet
```text
from mir_eval.util import filter_kwargs
# Use filter_kwargs to safely pass parameters to a metric function
score = filter_kwargs(metric_func, ref, est, **config_dict)
```

### Common Failure Modes
- Passing a non-callable object as the `_function` argument, which will raise a `TypeError`.
- Providing `*args` that do not match the positional argument requirements of the target `_function`, which will result in a `TypeError` from the underlying function call.
- Relying on `filter_kwargs` to handle positional argument mismatches; it only filters keyword arguments.

### Fix Code Hint
```python
# Ensure the function is callable and arguments are correctly structured
try:
    result = filter_kwargs(func, *args, **kwargs)
except TypeError as e:
    print(f"Function call failed: {e}")
```

## API Test: `first_n_target_proportion_R`

### Signature
```python
def first_n_target_proportion_R(reference_patterns, estimated_patterns, n=5)
```
_Source: source/mir_eval/pattern.py:554_

_Source doc:_ First n target proportion establishment recall metric. This metric is similar is similar to the establishment FPR score, but it only takes into account the first n estimated patterns and it only outputs the Recall value of it. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> R = mir_eval.pattern.first_n_target_proportion_R( ...                                 ref_patterns, est_patterns, n=5) Parameters ---------- reference_patterns : list The reference patterns in the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format n : int Number of patterns to consider from the estimated results, in the order they appear in the matrix. (Default value = 5) Returns ------- recall : float The first n target proportion Recall.

### Goal
Computes the recall of the "first n target proportion" metric for pattern discovery, evaluating how many reference patterns are successfully established by the top `n` estimated patterns.

### Parameters
- `reference_patterns`: A list of reference patterns, typically loaded via `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns, in the same format as `reference_patterns`.
- `n`, default `5`: The number of patterns to consider from the estimated results, ordered by their appearance in the input matrix.

### Input
The function expects `reference_patterns` and `estimated_patterns` to be lists of patterns formatted as returned by `mir_eval.io.load_patterns()`. Precondition: Ensure both inputs are loaded using the standard `mir_eval` I/O utilities to guarantee compatibility with the internal pattern matching logic.

### Output
Returns `recall` (float): The recall value representing the proportion of reference patterns established by the first `n` estimated patterns.

### Valid Call Patterns
```python
import mir_eval
# Inferred from source documentation
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
R = mir_eval.pattern.first_n_target_proportion_R(ref_patterns, est_patterns, n=5)
```

### LLM Instruction Prompt
- When calling `first_n_target_proportion_R`, ensure the inputs are lists of patterns derived from `mir_eval.io.load_patterns()`.
- Always specify the `n` parameter if the default of 5 is not appropriate for the specific evaluation context.
- The function is located in the `mir_eval.pattern` submodule.

### Prompt Snippet
```text
# Example usage of first_n_target_proportion_R
import mir_eval.pattern
recall = mir_eval.pattern.first_n_target_proportion_R(ref_data, est_data, n=10)
```

### Common Failure Modes
- **Format Mismatch:** Passing raw data that has not been processed by `mir_eval.io.load_patterns()` will likely cause the function to fail due to incompatible internal data structures.
- **Empty Inputs:** Providing empty lists for `reference_patterns` or `estimated_patterns` may result in division-by-zero errors or undefined recall values.
- **Index Out of Bounds:** If `n` is larger than the number of available `estimated_patterns`, the function may behave unexpectedly depending on the internal implementation of the pattern list slicing.

### Fix Code Hint
```python
# Ensure data is loaded correctly before evaluation
import mir_eval
try:
    ref = mir_eval.io.load_patterns("ref.txt")
    est = mir_eval.io.load_patterns("est.txt")
    score = mir_eval.pattern.first_n_target_proportion_R(ref, est, n=5)
except Exception as e:
    print(f"Evaluation failed: {e}")
```

## API Test: `first_n_three_layer_P`

### Signature
```python
def first_n_three_layer_P(reference_patterns, estimated_patterns, n=5)
```
_Source: source/mir_eval/pattern.py:509_

_Source doc:_ First n three-layer precision. This metric is basically the same as the three-layer FPR but it is only applied to the first n estimated patterns, and it only returns the precision. In MIREX and typically, n = 5. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> P = mir_eval.pattern.first_n_three_layer_P(ref_patterns, ...                                            est_patterns, n=5) Parameters ---------- reference_patterns : list The reference patterns in the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format n : int Number of patterns to consider from the estimated results, in the order they appear in the matrix (Default value = 5) Returns ------- precision : float The first n three-layer Precision

### Goal
Computes the precision of the first `n` estimated patterns by comparing them against a set of reference patterns using the three-layer evaluation metric.

### Parameters
- `reference_patterns`: A list of reference patterns, typically loaded via `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns, in the same format as the reference patterns.
- `n`, default `5`: The number of patterns to consider from the estimated results, ordered by their appearance in the input matrix.

### Input
The function expects `reference_patterns` and `estimated_patterns` to be lists of patterns formatted as returned by `mir_eval.io.load_patterns()`. Ensure that the input data is pre-validated and matches the expected structure for pattern discovery evaluation.

### Output
Returns `precision` (float): The calculated three-layer precision score for the first `n` estimated patterns.

### Valid Call Patterns
```python
import mir_eval
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
P = mir_eval.pattern.first_n_three_layer_P(ref_patterns, est_patterns, n=5)
```

### LLM Instruction Prompt
- When calling `first_n_three_layer_P`, ensure that both `reference_patterns` and `estimated_patterns` are loaded using `mir_eval.io.load_patterns()` to guarantee compatibility with the expected internal data structure. The `n` parameter should be an integer representing the subset of top-ranked patterns to evaluate.

### Prompt Snippet
```text
# Example usage of first_n_three_layer_P
precision = mir_eval.pattern.first_n_three_layer_P(ref_data, est_data, n=5)
```

### Common Failure Modes
- **Format Mismatch:** Passing raw data that has not been processed by `mir_eval.io.load_patterns()` will likely cause an internal error or incorrect metric calculation.
- **Empty Inputs:** Providing empty lists for either `reference_patterns` or `estimated_patterns` may result in undefined behavior or division by zero errors depending on the internal implementation of the precision calculation.
- **Index Out of Bounds:** If `n` is greater than the number of available patterns in `estimated_patterns`, the function may behave unexpectedly; ensure `n` is within the bounds of the provided data.

## API Test: `freq_to_voicing`

### Signature
```python
def freq_to_voicing(frequencies, voicing=None)
```
_Source: source/mir_eval/melody.py:157_

_Source doc:_ Convert from an array of frequency values to frequency array + voice/unvoiced array Parameters ---------- frequencies : np.ndarray Array of frequencies.  A frequency <= 0 indicates "unvoiced". voicing : np.ndarray Array of voicing values. (Default value = None) Default None, which means the voicing is inferred from `frequencies`: - frames with frequency <= 0.0 are considered "unvoiced" - frames with frequency > 0.0 are considered "voiced" If specified, `voicing` is used as the voicing array, but frequencies with value 0 are forced to have 0 voicing. - Voicing inferred by negative frequency values is ignored. Returns ------- frequencies : np.ndarray Array of frequencies, all >= 0. voiced : np.ndarray Array of voicings between 0 and 1, same length as frequencies, which indicates voiced or unvoiced

### Goal
Standardizes melody or pitch contour data by converting raw frequency arrays (where negative values indicate unvoiced frames) into a clean pair of non-negative frequency and explicit voicing probability arrays.

### Parameters
- `frequencies`: `np.ndarray`. An array of frequency values. Values <= 0.0 are interpreted as unvoiced.
- `voicing`, default `None`: `np.ndarray` or `None`. An array of voicing values between 0 and 1. If `None`, voicing is inferred from `frequencies` (<= 0.0 is unvoiced, > 0.0 is voiced).

### Input
- **Data Type:** `numpy.ndarray`.
- **Preconditions:** The input `frequencies` array must be a 1D NumPy array. If `voicing` is provided, it must have the same length as `frequencies`.

### Output
Returns a tuple of two `np.ndarray` objects:
- `frequencies`: An array of frequencies where all values are >= 0.
- `voiced`: An array of voicing values (0 to 1) corresponding to each frame.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Pattern 1: Infer voicing from frequency values
hz = np.array([0.0, 100.0, -132.0])
res_hz, res_voicing = mir_eval.melody.freq_to_voicing(hz)

# Pattern 2: Provide explicit voicing array
hz = np.array([0.0, 100.0, -132.0, 0, 131.0])
voicing = np.array([0.8, 0.0, 1.0, 0.0, 0.5])
res_hz, res_voicing = mir_eval.melody.freq_to_voicing(hz, voicing=voicing)
```

### LLM Instruction Prompt
- Always use `mir_eval.melody.freq_to_voicing` to ensure compatibility with the melody submodule.
- Ensure input arrays are `numpy.ndarray` types.
- When providing an explicit `voicing` array, remember that the function will automatically force voicing to 0 for any frame where the frequency is 0.

### Prompt Snippet
```text
# Convert raw frequency contour to mir_eval standard format
freqs, voiced = mir_eval.melody.freq_to_voicing(raw_frequencies, voicing=voicing_probs)
```

### Common Failure Modes
- **Shape Mismatch:** Passing `frequencies` and `voicing` arrays of different lengths will raise a `ValueError` (or internal NumPy broadcast error).
- **Invalid Types:** Passing lists instead of `np.ndarray` may cause unexpected behavior or errors in downstream metric calculations.
- **Zero-Frequency Override:** Users may be surprised that providing an explicit `voicing` value for a frame with `frequency == 0` is ignored and forced to 0.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays before calling
import numpy as np
frequencies = np.asarray(frequencies)
if voicing is not None:
    voicing = np.asarray(voicing)
res_hz, res_voicing = mir_eval.melody.freq_to_voicing(frequencies, voicing=voicing)
```

## API Test: `frequencies_to_midi`

### Signature
```python
def frequencies_to_midi(frequencies, ref_frequency=440.0)
```
_Source: source/mir_eval/multipitch.py:155_

_Source doc:_ Convert frequencies to continuous MIDI values. Parameters ---------- frequencies : list of np.ndarray Original frequency values ref_frequency : float reference frequency in Hz. Returns ------- frequencies_midi : list of np.ndarray Continuous MIDI frequency values.

### Goal
Converts raw frequency values (in Hz) into continuous MIDI note numbers to facilitate pitch-based evaluation in MIR tasks.

### Parameters
- `frequencies`: A list of `np.ndarray` objects containing the frequency values in Hz to be converted.
- `ref_frequency`, default `440.0`: The reference frequency in Hz used to define the MIDI tuning (default is A4 = 440.0 Hz).

### Input
The function expects a list of NumPy arrays containing numerical frequency data. Preconditions: The input must be a list of `np.ndarray` objects, even if the list contains only one array. The values should be positive floats representing frequency in Hz.

### Output
Returns a list of `np.ndarray` objects containing the corresponding continuous MIDI note values, where each MIDI value is calculated relative to the provided `ref_frequency`.

### Valid Call Patterns
```python
import numpy as np
import mir_eval

frequencies = [np.array([440.0, 220.0])]
midi_values = mir_eval.multipitch.frequencies_to_midi(frequencies)
```

### LLM Instruction Prompt
- When calling `frequencies_to_midi`, ensure the input is a list of `np.ndarray` objects.
- Always use the `mir_eval.multipitch` namespace to access this function.
- If the input frequencies are not in Hz, they must be converted before calling this function.

### Prompt Snippet
```text
# Convert frequencies to MIDI using mir_eval
midi_data = mir_eval.multipitch.frequencies_to_midi(frequency_list, ref_frequency=440.0)
```

### Common Failure Modes
- **Type Mismatch:** Passing a single `np.ndarray` instead of a list of `np.ndarray` objects will cause the function to iterate over the array elements incorrectly or raise an error.
- **Invalid Frequencies:** Passing non-positive frequency values (0 or negative) may result in mathematical errors (e.g., log of zero or negative numbers) during the MIDI conversion calculation.
- **Namespace Errors:** Attempting to call `frequencies_to_midi` directly without the `mir_eval.multipitch` prefix will result in an `AttributeError` or `NameError`.

### Fix Code Hint
```python
# Ensure input is a list of arrays
if not isinstance(frequencies, list):
    frequencies = [np.array(frequencies)]
midi_values = mir_eval.multipitch.frequencies_to_midi(frequencies)
```

## API Test: `generate_labels`

### Signature
```python
def generate_labels(items, prefix='__')
```
_Source: source/mir_eval/util.py:53_

_Source doc:_ Given an array of items (e.g. events, intervals), create a synthetic label for each event of the form '(label prefix)(item number)' Parameters ---------- items : list-like A list or array of events or intervals prefix : str This prefix will be prepended to all synthetically generated labels (Default value = '__') Returns ------- labels : list of str Synthetically generated labels

### Goal
Generates a list of unique, synthetic string labels for a collection of events or intervals to facilitate standardized identification in MIR evaluation tasks.

### Parameters
- `items`: A list-like object (e.g., a list or NumPy array) containing the events or intervals to be labeled.
- `prefix`, default `'__'`: A string to be prepended to each generated label.

### Input
The function expects a list-like structure containing the items to be labeled. No specific file format is required as the input is processed in-memory. Precondition: The input must be iterable to allow for index-based label generation.

### Output
Returns `list of str` — A list of strings where each element is formatted as `prefix` followed by the index of the item (e.g., `__0`, `__1`, etc.).

### Valid Call Patterns
```python
import mir_eval
# Inferred from signature:
labels = mir_eval.util.generate_labels(['event1', 'event2', 'event3'], prefix='beat_')
```

### LLM Instruction Prompt
- Use `mir_eval.util.generate_labels` when you need to create placeholder identifiers for event lists or interval arrays that lack descriptive labels.
- Always ensure the `items` argument is a list-like object.
- The returned labels are strictly formatted as `prefix` + `index`.

### Prompt Snippet
```text
# Generate synthetic labels for a list of detected onsets
labels = mir_eval.util.generate_labels(onset_times, prefix='onset_')
```

### Common Failure Modes
- Passing a non-iterable object as `items` will raise a `TypeError`.
- Providing an empty list for `items` will return an empty list, which may cause downstream indexing errors if the calling code expects at least one label.

### Fix Code Hint
```python
# Ensure items is a list or array before calling
if len(items) > 0:
    labels = mir_eval.util.generate_labels(items, prefix='event_')
else:
    labels = []
```

## API Test: `goto`

### Signature
```python
def goto(reference_beats, estimated_beats, goto_threshold=0.35, goto_mu=0.2, goto_sigma=0.2)
```
_Source: source/mir_eval/beat.py:229_

_Source doc:_ Calculate Goto's score, a binary 1 or 0 depending on some specific heuristic criteria Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> goto_score = mir_eval.beat.goto(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray query beat times, in seconds goto_threshold : float Threshold of beat error for a beat to be "correct" (Default value = 0.35) goto_mu : float The mean of the beat errors in the continuously correct track must be less than this (Default value = 0.2) goto_sigma : float The std of the beat errors in the continuously correct track must be less than this (Default value = 0.2) Returns ------- goto_score : float Either 1.0 or 0.0 if some specific criteria are met

### Goal
Calculates the Goto score, a binary metric that evaluates beat tracking performance based on specific heuristic criteria regarding the continuity and accuracy of beat estimates.

### Parameters
- `reference_beats`: `np.ndarray` containing ground-truth beat times in seconds.
- `estimated_beats`: `np.ndarray` containing the estimated beat times in seconds.
- `goto_threshold`, default `0.35`: The maximum allowable beat error (in seconds) for an individual beat to be considered "correct."
- `goto_mu`, default `0.2`: The maximum allowable mean of beat errors within a continuously correct track.
- `goto_sigma`, default `0.2`: The maximum allowable standard deviation of beat errors within a continuously correct track.

### Input
- **Data Types:** Expects `np.ndarray` objects.
- **Preconditions:** Input arrays should contain beat times in seconds. It is standard practice to preprocess inputs using `mir_eval.beat.trim_beats` before passing them to this function to ensure alignment and remove irrelevant segments.

### Output
Returns `float`: A binary score of `1.0` if the beat tracking meets the heuristic criteria, or `0.0` otherwise.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example from test suite
score = mir_eval.beat.goto(
    np.arange(100), 
    np.append(np.arange(80), np.arange(80, 100) + 0.2)
)
```

### LLM Instruction Prompt
- When calling `mir_eval.beat.goto`, ensure that both `reference_beats` and `estimated_beats` are provided as `np.ndarray` objects representing time in seconds.
- Always consider applying `mir_eval.beat.trim_beats` to your inputs prior to calling this function to ensure consistent evaluation.

### Prompt Snippet
```text
# Ensure inputs are trimmed before evaluation
ref = mir_eval.beat.trim_beats(reference_beats)
est = mir_eval.beat.trim_beats(estimated_beats)
score = mir_eval.beat.goto(ref, est)
```

### Common Failure Modes
- **Input Format:** Passing lists instead of `np.ndarray` objects may cause errors in internal calculations.
- **Unsorted Inputs:** If beat times are not sorted chronologically, the heuristic logic may produce unexpected results.
- **Empty Arrays:** Providing empty arrays for either reference or estimated beats will likely result in a `0.0` score or an internal validation error.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays and sorted
reference_beats = np.sort(np.array(reference_beats))
estimated_beats = np.sort(np.array(estimated_beats))
score = mir_eval.beat.goto(reference_beats, estimated_beats)
```

## API Test: `has_kwargs`

### Signature
```python
def has_kwargs(function)
```
_Source: source/mir_eval/util.py:838_

_Source doc:_ Determine whether a function has ``**kwargs``. Parameters ---------- function : callable The function to test Returns ------- True if function accepts arbitrary keyword arguments. False otherwise.

### Goal
Determine if a given callable accepts arbitrary keyword arguments (`**kwargs`), which is a common requirement for verifying compatibility with the `mir_eval` task-specific `evaluate()` function pattern.

### Parameters
- `function`: A callable object (e.g., function, method, or lambda) to be inspected for the presence of a `**kwargs` parameter.

### Input
- **Data:** A Python callable.
- **Preconditions:** The input must be a valid, inspectable callable object.

### Output
Returns `bool` — `True` if the function signature includes `**kwargs`, and `False` otherwise.

### Valid Call Patterns
```python
import mir_eval
# Check if a function accepts arbitrary keyword arguments
is_flexible = mir_eval.util.has_kwargs(my_function)
```

### LLM Instruction Prompt
- When using `mir_eval.util.has_kwargs`, ensure the input is a callable object. Use this utility to verify if a function can safely receive additional keyword arguments before passing them, which is critical when dynamically invoking task-specific `evaluate()` functions.

### Prompt Snippet
```text
mir_eval.util.has_kwargs(function)
```

### Common Failure Modes
- **Non-callable input:** Passing an object that is not a function or method will raise a `TypeError` during inspection.
- **Misinterpreting `*args`:** Note that `has_kwargs` specifically checks for `**kwargs` (keyword arguments); functions using only `*args` (positional variable arguments) will return `False`.
- **Built-in functions:** Some built-in functions or C-extensions may not expose their signature to inspection, potentially leading to unexpected results.

### Fix Code Hint
```python
# Ensure the target is a callable before checking
if callable(my_func):
    if mir_eval.util.has_kwargs(my_func):
        my_func(data, **extra_params)
    else:
        my_func(data)
```

## API Test: `hierarchy`

### Signature
```python
def hierarchy(intervals_hier, labels_hier, levels=None, ax=None, **kwargs)
```
_Source: source/mir_eval/display.py:498_

_Source doc:_ Plot a hierarchical segmentation Parameters ---------- intervals_hier : list of np.ndarray A list of segmentation intervals.  Each element should be an n-by-2 array of segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. Segmentations should be ordered by increasing specificity. labels_hier : list of list-like A list of segmentation labels.  Each element should be a list of labels for the corresponding element in `intervals_hier`. levels : list of string Each element ``levels[i]`` is a label for the ```i`` th segmentation. This is used in the legend to denote the levels in a segment hierarchy. ax : matplotlib.pyplot.axes An axis handle on which to draw the intervals. If none is provided, a new set of axes is created. **kwargs Additional keyword arguments to `labeled_intervals`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes hierarchical segmentation data by plotting multiple levels of segment intervals on a shared set of axes.

### Parameters
- `intervals_hier`: A list of `np.ndarray` objects, where each array is an n-by-2 matrix of segment start and end times. Segmentations must be ordered by increasing specificity.
- `labels_hier`: A list of list-like objects containing the labels corresponding to each interval in `intervals_hier`.
- `levels`, default `None`: A list of strings providing labels for each segmentation level, used to populate the plot legend.
- `ax`, default `None`: A `matplotlib.pyplot.axes` object. If not provided, a new figure and axes are generated.
- `**kwargs`: Additional keyword arguments passed directly to the `labeled_intervals` plotting function.

### Input
The function expects data formatted as segment intervals (start and end times). These are typically loaded using `mir_eval.io.load_labeled_intervals`. Precondition: The input lists must be ordered such that segmentations progress from coarse to fine (increasing specificity).

### Output
Returns `matplotlib.pyplot.axes._subplots.AxesSubplot`: A handle to the axes object containing the rendered hierarchical segmentation plot.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

# Basic usage without explicit level labels
int0, lab0 = mir_eval.io.load_labeled_intervals("ref00.lab")
int1, lab1 = mir_eval.io.load_labeled_intervals("ref01.lab")
mir_eval.display.hierarchy([int0, int1], [lab0, lab1])

# Usage with explicit level labels for the legend
mir_eval.display.hierarchy([int0, int1], [lab0, lab1], levels=["Large", "Small"])
```

### LLM Instruction Prompt
- When calling `mir_eval.display.hierarchy`, ensure that `intervals_hier` and `labels_hier` are lists of equal length.
- Always ensure the input intervals are ordered by increasing specificity (coarse to fine).
- If providing `levels`, ensure the length of the list matches the number of segmentations provided in `intervals_hier`.

### Prompt Snippet
```text
mir_eval.display.hierarchy(intervals_list, labels_list, levels=["Level 1", "Level 2"])
```

### Common Failure Modes
- **Ordering Mismatch:** Providing segmentations that are not ordered by increasing specificity, which results in confusing or overlapping visual representations.
- **Length Mismatch:** Providing a `levels` list that does not match the number of segmentations in `intervals_hier`, which will raise an index error or result in an incomplete legend.
- **Invalid Input Format:** Passing raw data that has not been processed into the n-by-2 interval format required by `mir_eval.io`.

### Fix Code Hint
```python
# Ensure intervals are sorted by start time and specificity before plotting
import numpy as np
# Example: Sort intervals by start time if they are not already
intervals = [i[i[:, 0].argsort()] for i in intervals_hier]
mir_eval.display.hierarchy(intervals, labels_hier, levels=["Coarse", "Fine"])
```

## API Test: `hz2cents`

### Signature
```python
def hz2cents(freq_hz, base_frequency=10.0)
```
_Source: source/mir_eval/melody.py:138_

_Source doc:_ Convert an array of frequency values in Hz to cents. 0 values are left in place. Parameters ---------- freq_hz : np.ndarray Array of frequencies in Hz. base_frequency : float Base frequency for conversion. (Default value = 10.0)

### Goal
Converts an array of frequency values from Hertz (Hz) to a cent scale relative to a specified base frequency, commonly used for pitch contour analysis in melody evaluation.

### Parameters
- `freq_hz`: `np.ndarray` containing frequency values in Hz.
- `base_frequency`, default `10.0`: `float` representing the reference frequency used as the zero-point for the cent conversion.

### Input
- **Data Type:** Requires a `numpy.ndarray` of frequency values.
- **Preconditions:** The input array should contain non-negative frequency values. The function is designed to handle `0.0` values by leaving them unchanged in the output.

### Output
Returns `np.ndarray` — An array of the same shape as `freq_hz` containing the converted values in cents.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example from test suite
hz = np.array([0.0, 10.0, 5.0, 320.0, 1420.31238974231])
cents = mir_eval.melody.hz2cents(hz)
```

### LLM Instruction Prompt
- Always use the `mir_eval.melody` namespace when calling this function.
- Ensure the input `freq_hz` is a `numpy.ndarray`.
- Remember that `0.0` values in the input are preserved as `0.0` in the output; do not attempt to perform logarithmic operations on these values manually.

### Prompt Snippet
```text
# Convert frequency array to cents using mir_eval
cents_array = mir_eval.melody.hz2cents(frequency_array, base_frequency=10.0)
```

### Common Failure Modes
- **Type Mismatch:** Passing a Python list instead of a `numpy.ndarray` may cause errors during internal array operations.
- **Negative Frequencies:** While the function handles `0.0`, passing negative frequency values may result in `NaN` or complex values due to the logarithmic nature of the cent conversion.
- **Namespace Errors:** Attempting to call `hz2cents` directly without the `mir_eval.melody` prefix will result in a `NameError`.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a numpy array
freq_data = np.array(my_frequency_list)
# Perform conversion
cents = mir_eval.melody.hz2cents(freq_data)
```

## API Test: `hz_to_midi`

### Signature
```python
def hz_to_midi(freqs)
```
_Source: source/mir_eval/util.py:913_

_Source doc:_ Convert Hz to MIDI numbers Parameters ---------- freqs : number or ndarray Frequency/frequencies in Hz Returns ------- midi : number or ndarray MIDI note numbers corresponding to input frequencies. Note that these may be fractional.

### Goal
Converts frequency values from Hertz (Hz) to MIDI note numbers to facilitate pitch-based evaluation and visualization in MIR tasks.

### Parameters
- `freqs`: A number or `numpy.ndarray` representing the frequency or frequencies in Hz to be converted.

### Input
- **Data Type:** Accepts scalar numbers or `numpy.ndarray` objects.
- **Preconditions:** The input `freqs` must contain positive values, as MIDI conversion is mathematically undefined for non-positive frequencies.

### Output
Returns `number or ndarray` — The corresponding MIDI note numbers. Note that these values may be fractional, representing microtonal deviations from standard equal temperament.

### Valid Call Patterns
```python
import mir_eval
# Converting an array of frequencies (e.g., from transcription data)
midi_values = mir_eval.util.hz_to_midi(freqs)
```

### LLM Instruction Prompt
- Always use `mir_eval.util.hz_to_midi` as the qualified path.
- Ensure the input `freqs` is a `numpy.ndarray` if processing batch transcription data, or a scalar if processing a single event.
- Do not assume the output will be an integer; always handle the return as a float or array of floats.

### Prompt Snippet
```text
# Example of converting frequency to MIDI for piano roll visualization
ref_midi = mir_eval.util.hz_to_midi(ref_p)
mir_eval.display.piano_roll(ref_t, midi=ref_midi, label="Reference")
```

### Common Failure Modes
- **Non-positive frequencies:** Passing 0 or negative values will result in mathematically invalid MIDI outputs (typically `-inf` or `NaN` depending on the implementation).
- **Type Mismatch:** Passing non-numeric types (e.g., strings or lists that cannot be cast to `numpy` arrays) will raise a `TypeError` or `ValueError` during the internal calculation.
- **Missing Import:** Attempting to call `hz_to_midi` directly without the `mir_eval.util` prefix will result in a `NameError`.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a numpy array to avoid scalar-specific errors
freqs = np.array([440.0, 880.0])
midi = mir_eval.util.hz_to_midi(freqs)
```

## API Test: `index_labels`

### Signature
```python
def index_labels(labels, case_sensitive=False)
```
_Source: source/mir_eval/util.py:14_

_Source doc:_ Convert a list of string identifiers into numerical indices. Parameters ---------- labels : list of strings, shape=(n,) A list of annotations, e.g., segment or chord labels from an annotation file. case_sensitive : bool Set to True to enable case-sensitive label indexing (Default value = False) Returns ------- indices : list, shape=(n,) Numerical representation of ``labels`` index_to_label : dict Mapping to convert numerical indices back to labels. ``labels[i] == index_to_label[indices[i]]``

### Goal
Converts a list of categorical string identifiers (such as chord or segment labels) into a sequence of numerical indices for use in quantitative evaluation tasks.

### Parameters
- `labels`: A list of strings of shape `(n,)` representing the annotations to be indexed.
- `case_sensitive`, default `False`: A boolean flag; if `True`, distinct casing (e.g., "A" vs "a") will result in different numerical indices.

### Input
The function expects a list of strings. These are typically extracted from annotation files loaded via `mir_eval.io` utilities. No specific file format is required as input, but the data must be provided as a Python list of strings.

### Output
Returns a tuple containing:
- `indices`: A list of integers of shape `(n,)` representing the numerical mapping of the input labels.
- `index_to_label`: A dictionary mapping the generated numerical indices back to their original string labels, satisfying the condition `labels[i] == index_to_label[indices[i]]`.

### Valid Call Patterns
```python
# Inferred from signature
indices, index_to_label = mir_eval.util.index_labels(labels, case_sensitive=False)
```

### LLM Instruction Prompt
- When calling `index_labels`, ensure the input `labels` is a flat list of strings.
- Always capture both return values: the numerical `indices` list and the `index_to_label` dictionary to ensure the mapping can be reversed.
- Use `case_sensitive=True` only if the specific MIR task distinguishes between label casing (e.g., major/minor chord notation).

### Prompt Snippet
```text
# Example of indexing chord labels
labels = ['C', 'C:maj', 'C', 'D:min']
indices, mapping = mir_eval.util.index_labels(labels)
```

### Common Failure Modes
- Passing a non-list or non-string iterable will result in a `TypeError` or `AttributeError` during processing.
- Assuming case-insensitivity when the task requires strict label matching; if `case_sensitive` is `False` (default), "C" and "c" will be treated as the same index.
- Attempting to use the returned `indices` list as a NumPy array without explicit conversion (the function returns a standard Python `list`).

### Fix Code Hint
```python
import numpy as np
# Convert to numpy array if needed for downstream numerical operations
indices = np.array(indices)
```

## API Test: `information_gain`

### Signature
```python
def information_gain(reference_beats, estimated_beats, bins=41)
```
_Source: source/mir_eval/beat.py:585_

_Source doc:_ Get the information gain - K-L divergence of the beat error histogram to a uniform histogram Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> information_gain = mir_eval.beat.information_gain(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray query beat times, in seconds bins : int Number of bins in the beat error histogram (Default value = 41) Returns ------- information_gain_score : float Entropy of beat error histogram

### Goal
Computes the information gain by calculating the Kullback-Leibler (K-L) divergence of the beat error histogram relative to a uniform distribution, providing a measure of beat tracking performance.

### Parameters
- `reference_beats`: `np.ndarray` containing reference beat times in seconds.
- `estimated_beats`: `np.ndarray` containing query (estimated) beat times in seconds.
- `bins`, default `41`: `int` representing the number of bins used to construct the beat error histogram.

### Input
The function expects two `np.ndarray` objects containing beat times in seconds. It is standard practice to preprocess these arrays using `mir_eval.beat.trim_beats` before evaluation to ensure alignment and remove extraneous data points.

### Output
Returns `information_gain_score` (float), which represents the entropy of the beat error histogram.

### Valid Call Patterns
```python
import mir_eval
# Inferred from source documentation
reference_beats = mir_eval.io.load_events('reference.txt')
estimated_beats = mir_eval.io.load_events('estimated.txt')
score = mir_eval.beat.information_gain(reference_beats, estimated_beats, bins=41)
```

### LLM Instruction Prompt
- Always ensure `reference_beats` and `estimated_beats` are provided as `np.ndarray` objects.
- Use `mir_eval.beat.trim_beats` on both inputs prior to calling this function to ensure consistent evaluation.
- The `bins` parameter must be an integer; the default is 41.

### Prompt Snippet
```text
# Example usage of information_gain
import mir_eval
ref = mir_eval.io.load_events('ref.txt')
est = mir_eval.io.load_events('est.txt')
ig_score = mir_eval.beat.information_gain(ref, est)
```

### Common Failure Modes
- **Input Type Mismatch:** Passing lists instead of `np.ndarray` objects may cause errors during internal processing.
- **Unprocessed Data:** Failing to trim beats (e.g., using `mir_eval.beat.trim_beats`) can lead to inaccurate divergence calculations if the reference and estimated arrays have different temporal extents.
- **Empty Arrays:** Providing empty arrays as input may result in invalid histogram calculations.

### Fix Code Hint
```python
# Ensure inputs are trimmed and formatted correctly
ref = mir_eval.beat.trim_beats(mir_eval.io.load_events('ref.txt'))
est = mir_eval.beat.trim_beats(mir_eval.io.load_events('est.txt'))
score = mir_eval.beat.information_gain(ref, est)
```

## API Test: `interpolate_intervals`

### Signature
```python
def interpolate_intervals(intervals, labels, time_points, fill_value=None)
```
_Source: source/mir_eval/util.py:118_

_Source doc:_ Assign labels to a set of points in time given a set of intervals. Time points that do not lie within an interval are mapped to `fill_value`. Parameters ---------- intervals : np.ndarray, shape=(n, 2) An array of time intervals, as returned by :func:`mir_eval.io.load_intervals()`. The ``i`` th interval spans time ``intervals[i, 0]`` to ``intervals[i, 1]``. Intervals are assumed to be disjoint. labels : list, shape=(n,) The annotation for each interval time_points : array_like, shape=(m,) Points in time to assign labels.  These must be in non-decreasing order. fill_value : type(labels[0]) Object to use for the label with out-of-range time points. (Default value = None) Returns ------- aligned_labels : list Labels corresponding to the given time points. Raises ------ ValueError If `time_points` is not in non-decreasing order.

### Goal
Maps discrete time points to corresponding labels based on a provided set of disjoint temporal intervals, useful for aligning continuous-time annotations with specific evaluation timestamps.

### Parameters
- `intervals`: `np.ndarray`, shape=(n, 2). An array of time intervals where each row `i` represents the start and end time of an interval.
- `labels`: `list`, shape=(n,). The annotation labels corresponding to each interval.
- `time_points`: `array_like`, shape=(m,). A sequence of time points to be labeled; must be provided in non-decreasing order.
- `fill_value`, default `None`: The value assigned to any `time_points` that do not fall within any of the provided `intervals`.

### Input
The function expects `intervals` to be disjoint and `time_points` to be sorted in non-decreasing order. `intervals` are typically obtained via `mir_eval.io.load_intervals()`.

### Output
Returns `aligned_labels` (a `list` of length `m`), where each element is the label associated with the corresponding time point, or the `fill_value` if the time point is outside all intervals.

### Valid Call Patterns
```python
from mir_eval import util
import numpy as np

# Example with standard intervals and labels
labels = list("abc")
intervals = np.array([(0.0, 1.0), (1.0, 2.0), (2.0, 3.0)])
time_points = [0.1, 1.5, 2.5]
aligned = util.interpolate_intervals(intervals, labels, time_points, fill_value="N")
```

### LLM Instruction Prompt
- Always ensure `time_points` are sorted in non-decreasing order before passing them to the function.
- Ensure `intervals` are disjoint; overlapping intervals are not supported by this function.
- Use the `fill_value` parameter to explicitly handle time points that fall outside the defined interval range.

### Prompt Snippet
```text
# Ensure time_points are sorted
time_points = np.sort(time_points)
aligned_labels = mir_eval.util.interpolate_intervals(intervals, labels, time_points, fill_value="N")
```

### Common Failure Modes
- `ValueError`: Raised if the `time_points` array is not in non-decreasing order.
- `IndexError` or logic errors: Occur if the length of `labels` does not match the number of rows in `intervals`.
- Unexpected `fill_value` behavior: If `fill_value` is not provided, out-of-range points will default to `None`, which may cause downstream type errors if the application expects a specific data type (e.g., strings or integers).

### Fix Code Hint
```python
# If time_points are unsorted, sort them first:
time_points = np.sort(time_points)
# Then call the function:
labels = util.interpolate_intervals(intervals, labels, time_points, fill_value="N")
```

## API Test: `intersect_files`

### Signature
```python
def intersect_files(flist1, flist2)
```
_Source: source/mir_eval/util.py:427_

_Source doc:_ Return the intersection of two sets of filepaths, based on the file name (after the final '/') and ignoring the file extension. Examples -------- >>> flist1 = ['/a/b/abc.lab', '/c/d/123.lab', '/e/f/xyz.lab'] >>> flist2 = ['/g/h/xyz.npy', '/i/j/123.txt', '/k/l/456.lab'] >>> sublist1, sublist2 = mir_eval.util.intersect_files(flist1, flist2) >>> print sublist1 ['/e/f/xyz.lab', '/c/d/123.lab'] >>> print sublist2 ['/g/h/xyz.npy', '/i/j/123.txt'] Parameters ---------- flist1 : list first list of filepaths flist2 : list second list of filepaths Returns ------- sublist1 : list subset of filepaths with matching stems from ``flist1`` sublist2 : list corresponding filepaths from ``flist2``

### Goal
Identify and extract pairs of corresponding files from two separate collections by matching their filenames while ignoring directory paths and file extensions.

### Parameters
- `flist1`: A list of strings representing the first collection of filepaths.
- `flist2`: A list of strings representing the second collection of filepaths.

### Input
The function expects two lists of strings, where each string is a valid file path. The matching logic is based on the "stem" of the filename (the portion after the final `/` and before the final extension). No specific file format is required, as the function operates purely on string manipulation of the provided paths.

### Output
Returns a tuple of two lists:
- `sublist1`: A subset of `flist1` containing only those paths whose stems were found in `flist2`.
- `sublist2`: A subset of `flist2` containing the corresponding paths that matched the stems in `flist1`.

### Valid Call Patterns
```python
from mir_eval import util

flist1 = ["/a/b/abc.lab", "/c/d/123.lab", "/e/f/xyz.lab"]
flist2 = ["/g/h/xyz.npy", "/i/j/123.txt", "/k/l/456.lab"]
sublist1, sublist2 = util.intersect_files(flist1, flist2)
```

### LLM Instruction Prompt
- When comparing two datasets of audio annotations or features, use `mir_eval.util.intersect_files` to ensure that only files present in both sets are processed.
- Always assign the result to a tuple of two variables (`sublist1, sublist2`) to capture the filtered results for both input lists.
- Note that the function ignores file extensions, so it is suitable for matching files across different formats (e.g., `.lab` vs `.npy`).

### Prompt Snippet
```text
# Filter file lists to ensure correspondence before evaluation
sublist1, sublist2 = mir_eval.util.intersect_files(reference_files, estimated_files)
```

### Common Failure Modes
- **Empty Intersections:** If no filenames match, the function returns two empty lists `([], [])`. Ensure your downstream code handles empty lists to avoid index errors.
- **Ambiguous Stems:** If multiple files in one list share the same stem (e.g., `/a/test.lab` and `/b/test.txt`), the behavior depends on the order of the input lists; ensure your file collections are unique by stem if strict 1:1 mapping is required.
- **Path Formatting:** The function relies on the final `/` to identify the filename; ensure paths are consistently formatted to avoid unexpected stem extraction.

### Fix Code Hint
```python
# Ensure lists are not empty before proceeding to evaluation
sublist1, sublist2 = mir_eval.util.intersect_files(flist1, flist2)
if not sublist1:
    print("Warning: No matching files found between the two lists.")
```

## API Test: `intervals_to_boundaries`

### Signature
```python
def intervals_to_boundaries(intervals, q=5)
```
_Source: source/mir_eval/util.py:221_

_Source doc:_ Convert interval times into boundaries. Parameters ---------- intervals : np.ndarray, shape=(n_events, 2) Array of interval start and end-times q : int Number of decimals to round to. (Default value = 5) Returns ------- boundaries : np.ndarray Interval boundary times, including the end of the final interval

### Goal
Converts a list of time intervals into a sorted, unique array of boundary time points, useful for segment-based evaluation tasks.

### Parameters
- `intervals`: A `np.ndarray` of shape `(n_events, 2)` containing the start and end times for each event.
- `q`, default `5`: An integer specifying the number of decimal places to which the boundary times should be rounded.

### Input
The input must be a NumPy array of shape `(n_events, 2)`. Preconditions include ensuring the array contains valid numerical time values. This function is typically used when processing segment annotations where intervals represent the duration of specific musical sections.

### Output
Returns a `np.ndarray` containing the unique, sorted boundary times extracted from the input intervals, including the end time of the final interval.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example inferred from signature
intervals = np.array([[0.0, 1.5], [1.5, 3.0], [3.0, 5.0]])
boundaries = mir_eval.util.intervals_to_boundaries(intervals, q=5)
```

### LLM Instruction Prompt
- When calling `intervals_to_boundaries`, ensure the input `intervals` is a NumPy array with two columns.
- The function is located in `mir_eval.util`.
- Use this function when you need to transform segment-based interval data into a set of discrete boundary points for evaluation.

### Prompt Snippet
```text
mir_eval.util.intervals_to_boundaries(intervals, q=5)
```

### Common Failure Modes
- Passing an array that does not have exactly two columns will result in an error.
- Providing non-numerical data types in the `intervals` array will cause a failure during processing.
- If the input `intervals` array is empty, the function may return an empty array or raise an error depending on the underlying NumPy operations.

### Fix Code Hint
```python
# Ensure intervals are a 2D numpy array before calling
import numpy as np
intervals = np.array(my_intervals_list)
if intervals.ndim == 2 and intervals.shape[1] == 2:
    boundaries = mir_eval.util.intervals_to_boundaries(intervals)
```

## API Test: `intervals_to_durations`

### Signature
```python
def intervals_to_durations(intervals)
```
_Source: source/mir_eval/util.py:892_

_Source doc:_ Convert an array of n intervals to their n durations. Parameters ---------- intervals : np.ndarray, shape=(n, 2) An array of time intervals, as returned by :func:`mir_eval.io.load_intervals()`. The ``i`` th interval spans time ``intervals[i, 0]`` to ``intervals[i, 1]``. Returns ------- durations : np.ndarray, shape=(n,) Array of the duration of each interval.

### Goal
Calculates the temporal length of each interval in a sequence, facilitating the conversion of start/end time pairs into duration-based representations for MIR evaluation tasks.

### Parameters
- `intervals`: A `np.ndarray` of shape `(n, 2)` representing `n` time intervals, where each row `i` contains the start time `intervals[i, 0]` and end time `intervals[i, 1]`.

### Input
The input must be a NumPy array of shape `(n, 2)`. It is typically obtained via `mir_eval.io.load_intervals()`. Precondition: The array must be structured such that the second column represents the end times and the first column represents the start times of the intervals.

### Output
Returns `np.ndarray` of shape `(n,)` containing the duration of each interval, calculated as `intervals[i, 1] - intervals[i, 0]`.

### Valid Call Patterns
```python
# Inferred from signature and documentation
import mir_eval
durations = mir_eval.util.intervals_to_durations(intervals)
```

### LLM Instruction Prompt
- When using `intervals_to_durations`, ensure the input is a NumPy array with exactly two columns.
- Always verify that the input array was loaded via `mir_eval.io.load_intervals()` or follows the same `(n, 2)` structure to ensure compatibility with the library's internal processing.

### Prompt Snippet
```text
mir_eval.util.intervals_to_durations(intervals)
```

### Common Failure Modes
- Passing an array with an incorrect number of columns (e.g., shape `(n,)` or `(n, 3)`), which will cause NumPy broadcasting errors or index errors.
- Providing intervals where the end time is less than the start time, which will result in negative duration values.
- Passing non-NumPy array types (e.g., lists) that do not support vectorized subtraction.

### Fix Code Hint
```python
import numpy as np
# Ensure input is a numpy array of shape (n, 2)
intervals = np.array(intervals)
durations = mir_eval.util.intervals_to_durations(intervals)
```

## API Test: `intervals_to_samples`

### Signature
```python
def intervals_to_samples(intervals, labels, offset=0, sample_size=0.1, fill_value=None)
```
_Source: source/mir_eval/util.py:74_

_Source doc:_ Convert an array of labeled time intervals to annotated samples. Parameters ---------- intervals : np.ndarray, shape=(n, d) An array of time intervals, as returned by :func:`mir_eval.io.load_intervals()` or :func:`mir_eval.io.load_labeled_intervals()`. The ``i`` th interval spans time ``intervals[i, 0]`` to ``intervals[i, 1]``. labels : list, shape=(n,) The annotation for each interval offset : float > 0 Phase offset of the sampled time grid (in seconds) (Default value = 0) sample_size : float > 0 duration of each sample to be generated (in seconds) (Default value = 0.1) fill_value : type(labels[0]) Object to use for the label with out-of-range time points. (Default value = None) Returns ------- sample_times : list list of sample times sample_labels : list array of labels for each generated sample Notes ----- Intervals will be rounded down to the nearest multiple of ``sample_size``.

### Goal
Discretizes continuous time-interval annotations into a fixed-rate sampled representation suitable for frame-based evaluation metrics.

### Parameters
- `intervals`: A `np.ndarray` of shape `(n, d)` representing time intervals, where each row `i` contains the start and end times for the `i`-th annotation.
- `labels`: A `list` of length `n` containing the annotation label corresponding to each interval.
- `offset`, default `0`: A `float` representing the phase offset of the sampled time grid in seconds.
- `sample_size`, default `0.1`: A `float` representing the duration of each generated sample in seconds.
- `fill_value`, default `None`: The value assigned to time points that fall outside the provided interval range.

### Input
The function expects `intervals` to be a NumPy array (typically generated via `mir_eval.io.load_intervals` or `mir_eval.io.load_labeled_intervals`) and `labels` to be a list of equal length. Precondition: `intervals` must be formatted as `(n, 2)` arrays where the first column is the start time and the second is the end time.

### Output
Returns a tuple containing:
- `sample_times`: A `list` of time points generated based on the `sample_size` and `offset`.
- `sample_labels`: A `list` of labels corresponding to each generated sample time.

### Valid Call Patterns
```python
from mir_eval import util
import numpy as np

labels = list("abc")
intervals = np.array([(n, n + 1.0) for n in range(len(labels))])

# Standard usage with custom sample size and fill value
sample_times, sample_labels = util.intervals_to_samples(
    intervals, labels, offset=0, sample_size=0.5, fill_value="N"
)
```

### LLM Instruction Prompt
- Always import `mir_eval.util` to access this function.
- Ensure `intervals` is a NumPy array of shape `(n, 2)`.
- Ensure `labels` is a list of length `n`.
- Remember that intervals are rounded down to the nearest multiple of `sample_size`.

### Prompt Snippet
```text
# Convert intervals to samples for frame-based evaluation
sample_times, sample_labels = mir_eval.util.intervals_to_samples(
    intervals, labels, offset=0.0, sample_size=0.1
)
```

### Common Failure Modes
- **Shape Mismatch:** Providing `intervals` and `labels` with different lengths will cause an error.
- **Invalid Array Format:** Passing a list instead of a `np.ndarray` for `intervals` may lead to indexing errors.
- **Rounding Assumptions:** Users often forget that intervals are rounded down to the nearest `sample_size`, which can lead to unexpected behavior at interval boundaries.

### Fix Code Hint
```python
# Ensure intervals are a numpy array before passing
intervals = np.array(intervals)
# Ensure labels match the number of intervals
assert len(intervals) == len(labels)
```

## API Test: `join`

### Signature
```python
def join(chord_root, quality='', extensions=None, bass='')
```
_Source: source/mir_eval/chord.py:436_

_Source doc:_ Join the parts of a chord into a complete chord label. Parameters ---------- chord_root : str Root pitch class of the chord, e.g. 'C', 'Eb' quality : str Quality of the chord, e.g. 'maj', 'hdim7' (Default value = '') extensions : list Any added or absent scaled degrees for this chord, e.g. ['4', '\*3'] (Default value = None) bass : str Scale degree of the bass note, e.g. '5'. (Default value = '') Returns ------- chord_label : str A complete chord label.

### Goal
Constructs a standardized string representation of a musical chord from its constituent components (root, quality, extensions, and bass).

### Parameters
- `chord_root`: The root pitch class of the chord (e.g., 'C', 'Eb').
- `quality`, default `''`: The quality of the chord (e.g., 'maj', 'hdim7').
- `extensions`, default `None`: A list of added or absent scale degrees (e.g., ['4', '*3']).
- `bass`, default `''`: The scale degree of the bass note (e.g., '5').

### Input
The function expects string inputs for root, quality, and bass, and a list of strings for extensions. No specific file I/O is required; inputs are standard Python primitives.

### Output
Returns `chord_label` (str): A single, complete chord label string formatted according to standard MIR conventions.

### Valid Call Patterns
```python
import mir_eval
# Example using positional arguments as verified in test_chord.py
label = mir_eval.chord.join('C', 'maj', ['7'], '5')
```

### LLM Instruction Prompt
- When generating chord labels, ensure the `chord_root` is a valid pitch class string.
- If `extensions` are provided, ensure they are passed as a list of strings.
- Use this function to normalize chord labels before comparison or evaluation tasks.

### Prompt Snippet
```text
# Construct a chord label using mir_eval.chord.join
label = mir_eval.chord.join(root, quality, extensions, bass)
```

### Common Failure Modes
- Passing a non-list type to the `extensions` parameter, which expects a list of strings.
- Providing invalid pitch class strings for `chord_root`, which may result in non-standard chord labels that fail downstream evaluation metrics.
- Omitting the `chord_root` argument, as it is a required positional parameter.

### Fix Code Hint
```python
# Ensure extensions are passed as a list, even for a single extension
label = mir_eval.chord.join('C', 'maj', ['7'], '5')
```

## API Test: `karaoke_perceptual_metric`

### Signature
```python
def karaoke_perceptual_metric(reference_timestamps, estimated_timestamps)
```
_Source: source/mir_eval/alignment.py:269_

_Source doc:_ Metric based on human synchronicity perception as measured in the paper "User-centered evaluation of lyrics to audio alignment" [#lizemasclef2021] The parameters of this function were tuned on data collected through a user Karaoke-like experiment It reflects human judgment of how "synchronous" lyrics and audio stimuli are perceived in that setup. Beware that this metric is non-symmetrical and by construction it is also not equal to 1 at 0. Examples -------- >>> reference_timestamps = mir_eval.io.load_events('reference.txt') >>> estimated_timestamps = mir_eval.io.load_events('estimated.txt') >>> score = mir_eval.align.karaoke_perceptual_metric(reference_onsets, estimated_timestamps) Parameters ---------- reference_timestamps : np.ndarray reference timestamps, in seconds estimated_timestamps : np.ndarray estimated timestamps, in seconds Returns ------- perceptual_score : float Perceptual score, averaged over all timestamps

### Goal
Computes a perceptual synchronicity score for lyrics-to-audio alignment based on human judgment models derived from karaoke-style user experiments.

### Parameters
- `reference_timestamps`: `np.ndarray` containing the ground-truth event timestamps in seconds.
- `estimated_timestamps`: `np.ndarray` containing the predicted event timestamps in seconds.

### Input
The function expects two NumPy arrays representing timestamps in seconds. These are typically loaded using `mir_eval.io.load_events`. Preconditions include ensuring both arrays are formatted as one-dimensional arrays of floats. Note that this metric is non-symmetrical; swapping the reference and estimated inputs will yield different results.

### Output
Returns `perceptual_score` (float): The calculated perceptual score, representing the average synchronicity across all provided timestamps.

### Valid Call Patterns
```python
import mir_eval
# Example inferred from documentation and standard mir_eval patterns
reference_timestamps = mir_eval.io.load_events('reference.txt')
estimated_timestamps = mir_eval.io.load_events('estimated.txt')
score = mir_eval.align.karaoke_perceptual_metric(reference_timestamps, estimated_timestamps)
```

### LLM Instruction Prompt
- Always use `mir_eval.align.karaoke_perceptual_metric` as the function path.
- Ensure inputs are `np.ndarray` objects containing timestamps in seconds.
- Remember that the metric is non-symmetrical; ensure the ground truth is passed as the first argument and the estimate as the second.

### Prompt Snippet
```text
# Calculate the karaoke perceptual score
score = mir_eval.align.karaoke_perceptual_metric(ref_data, est_data)
```

### Common Failure Modes
- **Non-symmetrical results:** Users may incorrectly assume the metric is symmetric; swapping inputs will change the output.
- **Input format:** Passing lists instead of `np.ndarray` may cause internal validation errors.
- **Zero-offset expectation:** Users may expect a score of 1.0 when timestamps are identical; the documentation explicitly warns that the metric is not equal to 1 at 0.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays
ref = np.array(reference_list)
est = np.array(estimated_list)

# Compute metric
score = mir_eval.align.karaoke_perceptual_metric(ref, est)
```

## API Test: `labeled_intervals`

### Signature
```python
def labeled_intervals(intervals, labels, label_set=None, base=None, height=None, extend_labels=True, ax=None, tick=True, prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:312_

_Source doc:_ Plot labeled intervals with each label on its own row. Parameters ---------- intervals : np.ndarray, shape=(n, 2) segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. label_set : list An (ordered) list of labels to determine the plotting order. If not provided, the labels will be inferred from ``ax.get_yticklabels()``. If no ``yticklabels`` exist, then the sorted set of unique values in ``labels`` is taken as the label set. base : np.ndarray, shape=(n,), optional Vertical positions of each label. By default, labels are positioned at integers ``np.arange(len(labels))``. height : scalar or np.ndarray, shape=(n,), optional Height for each label. If scalar, the same value is applied to all labels. By default, each label has ``height=1``. extend_labels : bool If ``False``, only values of ``labels`` that also exist in ``label_set`` will be shown. If ``True``, all labels are shown, with those in `labels` but not in ``label_set`` appended to the top of the plot. A horizontal line is drawn to indicate the separation between values in or out of ``label_set``. ax : matplotlib.pyplot.axes An axis handle on which to draw the intervals. If none is provided, a new set of axes is created. tick : bool If ``True``, sets tick positions and labels on the y-axis. prop_cycle : cycle.Cycler An optional property cycle object to specify style properties. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to pass to `matplotlib.collection.PolyCollection`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes segment-based annotations (such as chords or structural segments) by plotting intervals as horizontal bars on a per-label row basis.

### Parameters
- `intervals`: A `np.ndarray` of shape `(n, 2)` representing the start and end times of segments.
- `labels`: A `list` of length `n` containing the string labels corresponding to each interval.
- `label_set`, default `None`: An ordered list of labels defining the vertical plotting order.
- `base`, default `None`: A `np.ndarray` of shape `(n,)` defining the vertical y-axis position for each interval.
- `height`, default `None`: A scalar or `np.ndarray` of shape `(n,)` defining the vertical thickness of the bars.
- `extend_labels`, default `True`: If `True`, labels not in `label_set` are appended to the top of the plot; if `False`, they are excluded.
- `ax`, default `None`: A `matplotlib.pyplot.axes` object to draw on; if `None`, a new figure is created.
- `tick`, default `True`: If `True`, automatically configures y-axis ticks and labels.
- `prop_cycle`, default `None`: A `matplotlib.cycler.Cycler` object for styling; defaults to the current matplotlib cycle.
- `**kwargs`: Additional keyword arguments passed directly to `matplotlib.collection.PolyCollection`.

### Input
Requires `intervals` and `labels` to be pre-loaded, typically using `mir_eval.io.load_labeled_intervals`. The data must be in a format compatible with standard MIR annotation files (e.g., tab-separated start/end/label text files).

### Output
Returns a `matplotlib.pyplot.axes._subplots.AxesSubplot` object containing the rendered visualization.

### Valid Call Patterns
```python
# Basic usage
mir_eval.display.labeled_intervals(intervals, labels)

# Usage with specific axis and filtering
ax = plt.axes()
mir_eval.display.labeled_intervals(
    intervals, labels, label_set=[], extend_labels=False, ax=ax
)
```

### LLM Instruction Prompt
- Always use `mir_eval.display.labeled_intervals` as the entry point.
- Ensure `intervals` is a `np.ndarray` of shape `(n, 2)` and `labels` is a list of length `n`.
- If providing a custom `ax`, ensure it is a valid `matplotlib` axes object.
- When `extend_labels` is `False`, ensure `label_set` is provided to avoid empty plots if no labels match.

### Prompt Snippet
```text
import mir_eval.display
import matplotlib.pyplot as plt
# ... load data ...
mir_eval.display.labeled_intervals(intervals, labels)
```

### Common Failure Modes
- Passing `intervals` or `labels` with mismatched lengths, which will cause internal indexing errors.
- Providing `label_set` as `None` while expecting a specific vertical order, which defaults to alphabetical sorting of unique labels.
- Attempting to use the function without a valid `matplotlib` backend in a headless environment (ensure `plt.switch_backend('Agg')` is called if necessary).

### Fix Code Hint
```python
# Ensure data is loaded correctly before plotting
intervals, labels = mir_eval.io.load_labeled_intervals("path/to/file.lab")
# If plotting fails due to backend, set Agg before importing pyplot
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
```

## API Test: `lmeasure`

### Signature
```python
def lmeasure(reference_intervals_hier, reference_labels_hier, estimated_intervals_hier, estimated_labels_hier, frame_size=0.1, beta=1.0)
```
_Source: source/mir_eval/hierarchy.py:548_

_Source doc:_ Compute the tree measures for hierarchical segment annotations. Parameters ---------- reference_intervals_hier : list of ndarray ``reference_intervals_hier[i]`` contains the segment intervals (in seconds) for the ``i`` th layer of the annotations.  Layers are ordered from top to bottom, so that the last list of intervals should be the most specific. reference_labels_hier : list of list of str ``reference_labels_hier[i]`` contains the segment labels for the ``i`` th layer of the annotations estimated_intervals_hier : list of ndarray estimated_labels_hier : list of ndarray Like ``reference_intervals_hier`` and ``reference_labels_hier`` but for the estimated annotation frame_size : float > 0 length (in seconds) of frames.  The frame size cannot be longer than the window. beta : float > 0 beta parameter for the F-measure. Returns ------- l_precision : number [0, 1] L-measure Precision l_recall : number [0, 1] L-measure Recall l_measure : number [0, 1] F-beta measure for ``(l_precision, l_recall)`` Raises ------ ValueError If either of the input hierarchies are inconsistent If the input hierarchies have different time durations If ``frame_size > window`` or ``frame_size <= 0``

### Goal
Computes the L-measure, a hierarchical F-measure metric used to evaluate the accuracy of multi-level segment annotations by comparing estimated hierarchical structures against a ground-truth reference.

### Parameters
- `reference_intervals_hier`: A list of `np.ndarray` objects, where each array contains segment intervals (in seconds) for a specific layer of the hierarchy, ordered from top (coarse) to bottom (fine).
- `reference_labels_hier`: A list of lists of strings, where each inner list contains the segment labels corresponding to the intervals in the matching layer of `reference_intervals_hier`.
- `estimated_intervals_hier`: A list of `np.ndarray` objects representing the estimated segment intervals, following the same hierarchical structure as the reference.
- `estimated_labels_hier`: A list of lists of strings representing the estimated segment labels, following the same hierarchical structure as the reference.
- `frame_size`, default `0.1`: The length of frames in seconds used for discretization; must be greater than 0 and less than or equal to the window size.
- `beta`, default `1.0`: The beta parameter for the F-measure, determining the weighting of precision versus recall.

### Input
The function expects hierarchical segment data. Intervals should be provided as `np.ndarray` objects (typically shape `(N, 2)`). Labels should be provided as lists of strings. Precondition: The hierarchies must be consistent (boundaries in finer layers must align with coarser layers) and both reference and estimate must cover the same total time duration.

### Output
Returns a tuple of three floats: `(l_precision, l_recall, l_measure)`, representing the L-measure precision, recall, and the F-beta score respectively, all in the range `[0, 1]`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example based on test_lmeasure_pass
ref = [np.asarray([[0, 30]]), np.asarray([[0, 15], [15, 30]])]
ref_lab = [["A"], ["a", "b"]]
est = [np.asarray([[0, 30]])]
est_lab = [["A"]]

l_precision, l_recall, l_measure = mir_eval.hierarchy.lmeasure(
    ref, ref_lab, est, est_lab, frame_size=0.1
)
```

### LLM Instruction Prompt
- When calling `lmeasure`, ensure that `reference_intervals_hier` and `estimated_intervals_hier` are lists of `numpy.ndarray` objects.
- Ensure that the number of layers in the reference and estimate match.
- Always validate that the hierarchy is consistent (finer layers must be sub-segments of coarser layers) to avoid `ValueError` or `UserWarning`.

### Prompt Snippet
```text
# Ensure inputs are formatted as lists of ndarrays
ref_intervals = [np.array([[0, 10]]), np.array([[0, 5], [5, 10]])]
# Call the hierarchy metric
l_prec, l_rec, l_f = mir_eval.hierarchy.lmeasure(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- `ValueError`: Raised if the input hierarchies are inconsistent (e.g., a segment in a lower layer is not contained within a segment in the layer above).
- `ValueError`: Raised if the total duration of the reference hierarchy does not match the total duration of the estimated hierarchy.
- `ValueError`: Raised if `frame_size` is non-positive or exceeds the window size.
- `UserWarning`: Triggered if there are missing boundaries between layers, indicating an inconsistent hierarchy.

### Fix Code Hint
```python
# Ensure all intervals are numpy arrays before passing to lmeasure
reference_intervals_hier = [np.asarray(layer) for layer in reference_intervals_hier]
estimated_intervals_hier = [np.asarray(layer) for layer in estimated_intervals_hier]
```

## API Test: `load_delimited`

### Signature
```python
def load_delimited(filename, converters, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:32_

### Goal
Loads data from a delimited text file into a set of arrays by applying specific conversion functions to each column.

### Parameters
- `filename`: A string path to the file, a `pathlib.Path` object, or an open file descriptor.
- `converters`: A list of functions (e.g., `int`, `float`) used to convert each column of the delimited file into the desired data type.
- `delimiter`, default `'\\s+'`: A regular expression string defining the delimiter between columns.
- `comment`, default `'#'`: The character used to denote comment lines in the file; lines starting with this character are ignored. Set to `None` to disable comment filtering.

### Input
The function expects a text-based file where data is organized in columns separated by the specified delimiter. Preconditions include:
- The number of columns in the file must match the length of the `converters` list.
- Each column must be successfully parsable by the corresponding function provided in `converters`.
- The file must be accessible for reading.

### Output
Returns a tuple of arrays, where each array contains the parsed data for the corresponding column.

### Valid Call Patterns
```python
import mir_eval
import io

# Loading two columns of integers with a custom comment character
# Example based on test_load_delimited_commented
col1, col2 = mir_eval.io.load_delimited(f, [int, int], comment=";")
```

### LLM Instruction Prompt
- When using `load_delimited`, ensure the `converters` list length exactly matches the number of columns in the input file.
- Always provide a `converters` list containing callable functions (e.g., `[float, float]`).
- If the input file uses a non-standard comment character, explicitly set the `comment` parameter to avoid `ValueError` during parsing.

### Prompt Snippet
```text
# Example of loading a 2-column float file
data = mir_eval.io.load_delimited('data.txt', [float, float])
```

### Common Failure Modes
- `IOError`: Raised if the `filename` is invalid or cannot be opened.
- `ValueError`: Raised if the number of columns in the file does not match the length of the `converters` list.
- `ValueError`: Raised if a value in the file cannot be converted by the corresponding function in `converters`.
- `ValueError`: Raised if the file contains comment characters that are not accounted for by the `comment` parameter.

### Fix Code Hint
```python
# Ensure the number of converters matches the file structure
# If the file has 3 columns, provide 3 converters
data = mir_eval.io.load_delimited(filename, [float, float, float])

# If parsing fails, verify the delimiter matches the file format
data = mir_eval.io.load_delimited(filename, [float, float], delimiter=',')
```

## API Test: `load_events`

### Signature
```python
def load_events(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:123_

### Goal
Loads time-stamped event data from a text file into a NumPy array for use in MIR evaluation tasks such as beat or onset tracking.

### Parameters
- `filename`: The path to the annotation file (supports `str`, `pathlib.Path`, or `os.PathLike` objects).
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text-based file containing time-stamped events. Each line should represent an event, typically formatted as a single column of time values (floats). The file must be accessible by the local filesystem. Preconditions include ensuring the file format matches the expected structure for the specific MIR task (e.g., beat or onset times).

### Output
Returns `unspecified` — A NumPy array containing the parsed event times as floating-point values.

### Valid Call Patterns
```python
import mir_eval
# Standard usage for loading beat or onset annotations
beats = mir_eval.io.load_events("data/beat/ref00.txt")

# Usage with custom delimiters or comment characters
events = mir_eval.io.load_events("data/custom.txt", delimiter=',', comment='%')
```

### LLM Instruction Prompt
- When calling `load_events`, always provide the `filename` as the first argument.
- Use `mir_eval.io.load_events` as the fully qualified name.
- Ensure the input file is a plain text file containing numeric event times.
- If the file uses a non-standard separator, explicitly define the `delimiter` parameter.

### Prompt Snippet
```text
# Load reference and estimated event files
ref_events = mir_eval.io.load_events(reference_path)
est_events = mir_eval.io.load_events(estimated_path)
```

### Common Failure Modes
- **FileNotFoundError**: Occurs if the provided `filename` path is incorrect or the file does not exist.
- **ValueError**: Occurs if the file contains non-numeric data that cannot be parsed into floats, or if the `delimiter` does not match the file structure.
- **Empty File**: If the file is empty or contains only comments, the function will return an empty NumPy array, which may cause downstream metric functions to raise errors if they expect non-empty input.

### Fix Code Hint
```python
import os
import mir_eval

# Ensure file exists before loading
if os.path.exists(filename):
    events = mir_eval.io.load_events(filename)
else:
    print(f"File {filename} not found.")
```

## API Test: `load_intervals`

### Signature
```python
def load_intervals(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:206_

### Goal
Loads interval data (start and end times) from a delimited text file for use in MIR evaluation tasks such as segmentation.

### Parameters
- `filename`: The path to the file to be loaded, or an open file descriptor. Supports `str`, `pathlib.Path`, or `os.PathLike` objects.
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file where each line represents an interval, typically containing two columns (start time and end time). The input must be formatted such that the data can be parsed into a numerical array. Preconditions include ensuring the file exists and is accessible; the function handles `pathlib.Path` and `os.PathLike` objects.

### Output
Returns `unspecified` — Returns a NumPy array of shape `(N, 2)` containing the start and end times of the intervals.

### Valid Call Patterns
```python
import mir_eval
# Loading from a file path
intervals = mir_eval.io.load_intervals('segments.txt')

# Loading from an open file descriptor
with open('segments.txt', 'r') as f:
    intervals = mir_eval.io.load_intervals(f)
```

### LLM Instruction Prompt
- Use `mir_eval.io.load_intervals` when loading segment or interval-based annotations.
- Always ensure the input file contains at least two columns representing start and end times.
- If the file uses a non-standard delimiter (e.g., commas), explicitly pass the `delimiter` argument.

### Prompt Snippet
```text
# Load interval data for segmentation evaluation
intervals = mir_eval.io.load_intervals(file_path)
```

### Common Failure Modes
- **Non-increasing intervals:** Providing intervals where the end time is less than the start time will trigger a `UserWarning` but will still return the array.
- **Malformed files:** Files with inconsistent column counts or non-numeric data will raise parsing errors.
- **Missing file:** Providing a `filename` that does not exist will raise a `FileNotFoundError`.

### Fix Code Hint
```python
import mir_eval
import warnings

# Ensure intervals are valid (start < end) if necessary
intervals = mir_eval.io.load_intervals('data.txt')
if not np.all(intervals[:, 1] > intervals[:, 0]):
    warnings.warn("Detected intervals with non-positive duration.")
```

## API Test: `load_key`

### Signature
```python
def load_key(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:497_

### Goal
Loads key annotation data from a text-based file into a format suitable for key evaluation metrics.

### Parameters
- `filename`: The path to the annotation file (supports `str`, `pathlib.Path`, or `os.PathLike` objects).
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file containing key annotations. The file should be formatted such that it can be parsed by the specified delimiter, with comment lines prefixed by the `comment` character. Preconditions include ensuring the file exists and is accessible by the current process.

### Output
Returns `unspecified` — The function returns the loaded key data, typically structured as a list of tuples or a NumPy array representing the start time, end time, and key label for each segment.

### Valid Call Patterns
```python
import mir_eval
# Inferred from signature: loading a standard key annotation file
key_data = mir_eval.io.load_key('path/to/key_annotations.txt')

# Loading with custom delimiter and comment character
key_data = mir_eval.io.load_key('annotations.csv', delimiter=',', comment='%')
```

### LLM Instruction Prompt
- When calling `mir_eval.io.load_key`, always provide the `filename` as the first argument.
- If the input file uses a non-standard format, explicitly define the `delimiter` and `comment` parameters to ensure correct parsing.
- Ensure the file path is provided as a string or a `pathlib.Path` object.

### Prompt Snippet
```text
# Example of loading key annotations
import mir_eval.io
key_data = mir_eval.io.load_key(filename='data/key.txt')
```

### Common Failure Modes
- **FileNotFoundError**: Occurs if the provided `filename` path is incorrect or the file does not exist.
- **Parsing Errors**: Occurs if the `delimiter` does not match the actual structure of the file, leading to incorrect column splitting.
- **PermissionError**: Occurs if the script lacks read access to the specified file.

### Fix Code Hint
```python
import os
# Verify file existence before loading
if os.path.exists(filename):
    data = mir_eval.io.load_key(filename)
else:
    print(f"File {filename} not found.")
```

## API Test: `load_labeled_events`

### Signature
```python
def load_labeled_events(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:162_

### Goal
Loads time-stamped labeled events (such as melody pitch contours) from a delimited text file into a pair of NumPy arrays.

### Parameters
- `filename`: The path to the text file containing the labeled events. Accepts strings, `pathlib.Path`, or `os.PathLike` objects.
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file where each line represents an event, typically formatted as `time label`. The file must be structured such that the first column is parsable as a float (time) and the subsequent column(s) represent the associated label or value. Preconditions include ensuring the file exists and that the delimiter matches the file's internal structure.

### Output
Returns a tuple of two NumPy arrays: the first array contains the event times (floats), and the second array contains the corresponding labels or values.

### Valid Call Patterns
```python
from mir_eval.io import load_labeled_events

# Loading melody pitch data from a text file
times, freqs = load_labeled_events("data/melody/ref00.txt")
```

### LLM Instruction Prompt
- Use `load_labeled_events` when parsing time-series data where each row contains a timestamp followed by a value (e.g., melody pitch contours).
- Always assign the result to a tuple of two variables: `times, labels = load_labeled_events(...)`.
- Ensure the `delimiter` matches the specific formatting of the source file if it deviates from standard whitespace.

### Prompt Snippet
```text
times, values = mir_eval.io.load_labeled_events(filename)
```

### Common Failure Modes
- **Delimiter Mismatch:** Providing a default delimiter when the file uses a specific character (like a comma or tab) will cause parsing errors or incorrect array shapes.
- **Malformed Lines:** Lines that do not conform to the expected `time label` structure (e.g., missing columns or non-numeric timestamps) will raise a `ValueError` or `IndexError`.
- **File Not Found:** Passing a non-existent path will raise an `IOError` or `FileNotFoundError`.

### Fix Code Hint
```python
# If parsing a CSV file, explicitly set the delimiter
times, labels = mir_eval.io.load_labeled_events("data.csv", delimiter=",")

# If the file contains header lines, ensure they start with the comment character
# or pre-process the file to remove them before loading.
```

## API Test: `load_labeled_intervals`

### Signature
```python
def load_labeled_intervals(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:248_

### Goal
Loads segment-based annotations from a text file, returning a tuple of interval boundaries and their corresponding labels for use in structural analysis and segmentation evaluation.

### Parameters
- `filename`: The path to the annotation file (supports `str`, `pathlib.Path`, or `os.PathLike` objects).
- `delimiter`, default `'\\s+'`: The regular expression pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file where each line represents an interval and a label. Typically, this follows a format of `start_time end_time label`. The file must be formatted such that the delimiter correctly separates these fields. Preconditions include ensuring the file exists and that the data is structured consistently with the expected interval-label format required by `mir_eval` segment evaluation tasks.

### Output
Returns a tuple `(intervals, labels)`:
- `intervals`: A NumPy array of shape `(n, 2)` containing the start and end times of each segment.
- `labels`: A list or array of length `n` containing the string labels associated with each interval.

### Valid Call Patterns
```python
import mir_eval
# Standard usage as seen in test suite
intervals, labels = mir_eval.io.load_labeled_intervals("data/segment/ref00.lab")
```

### LLM Instruction Prompt
- When calling `load_labeled_intervals`, always assign the result to a tuple of two variables: `intervals` and `labels`.
- Ensure the `filename` provided is a valid path to a text-based annotation file.
- If the input file uses a non-standard delimiter (e.g., commas), explicitly pass the `delimiter` argument.

### Prompt Snippet
```text
# Load reference segments for evaluation
intervals, labels = mir_eval.io.load_labeled_intervals(path_to_file)
```

### Common Failure Modes
- **FileNotFoundError**: Occurs if the provided `filename` path is incorrect or the file is inaccessible.
- **ValueError**: Triggered if the file content does not conform to the expected structure (e.g., lines with insufficient columns or non-numeric start/end times).
- **Delimiter Mismatch**: If the `delimiter` does not match the file structure, the parser may fail to split columns correctly, leading to empty or malformed arrays.

### Fix Code Hint
```python
# Ensure the file exists and check the delimiter if parsing fails
import os
if os.path.exists(filename):
    intervals, labels = mir_eval.io.load_labeled_intervals(filename, delimiter='\t')
```

## API Test: `load_patterns`

### Signature
```python
def load_patterns(filename)
```
_Source: source/mir_eval/io.py:331_

_Source doc:_ Load the patterns contained in the filename and puts them into a list of patterns, each pattern being a list of occurrence, and each occurrence being a list of (onset, midi) pairs. The input file must be formatted as described in MIREX 2013: http://www.music-ir.org/mirex/wiki/2013:Discovery_of_Repeated_Themes_%26_Sections Parameters ---------- filename : str or `os.Pathlike` The input file path containing the patterns of a given piece using the MIREX 2013 format. Returns ------- pattern_list : list The list of patterns, containing all their occurrences, using the following format:: onset_midi = (onset_time, midi_number) occurrence = [onset_midi1, ..., onset_midiO] pattern = [occurrence1, ..., occurrenceM] pattern_list = [pattern1, ..., patternN] where ``N`` is the number of patterns, ``M[i]`` is the number of occurrences of the ``i`` th pattern, and ``O[j]`` is the number of onsets in the ``j``'th occurrence.  E.g.:: occ1 = [(0.5, 67.0), (1.0, 67.0), (1.5, 67.0), (2.0, 64.0)] occ2 = [(4.5, 65.0), (5.0, 65.0), (5.5, 65.0), (6.0, 62.0)] pattern1 = [occ1, occ2] occ1 = [(10.5, 67.0), (11.0, 67.0), (11.5, 67.0), (12.0, 64.0), (12.5, 69.0), (13.0, 69.0), (13.5, 69.0), (14.0, 67.0), (14.5, 76.0), (15.0, 76.0), (15.5, 76.0), (16.0, 72.0)] occ2 = [(18.5, 67.0), (19.0, 67.0), (19.5, 67.0), (20.0, 62.0), (20.5, 69.0), (21.0, 69.0), (21.5, 69.0), (22.0, 67.0), (22.5, 77.0), (23.0, 77.0), (23.5, 77.0), (24.0, 74.0)] pattern2 = [occ1, occ2] pattern_list = [pattern1, pattern2]

### Goal
Loads repeated musical themes or sections from a file into a structured nested list format for pattern discovery evaluation.

### Parameters
- `filename`: A string or `os.PathLike` object representing the path to the input file containing pattern data.

### Input
The input file must strictly adhere to the MIREX 2013 "Discovery of Repeated Themes & Sections" format. The function expects the file to be accessible at the provided path.

### Output
Returns a `list` of patterns (`pattern_list`). Each pattern is a `list` of occurrences, and each occurrence is a `list` of `(onset_time, midi_number)` tuples.

### Valid Call Patterns
```python
import mir_eval
# Inferred from signature
patterns = mir_eval.io.load_patterns('path/to/patterns.txt')
```

### LLM Instruction Prompt
- Use `mir_eval.io.load_patterns` to parse files formatted for the MIREX 2013 Discovery of Repeated Themes & Sections task.
- Ensure the input path is a valid string or `os.PathLike` object.
- The returned object is a nested list structure: `list[list[list[tuple[float, float]]]]`.

### Prompt Snippet
```text
# Load patterns for evaluation
patterns = mir_eval.io.load_patterns(filename)
```

### Common Failure Modes
- Providing a file that does not conform to the MIREX 2013 format, which will result in parsing errors or incorrectly structured lists.
- Passing a non-existent file path, which will raise an `IOError` or `FileNotFoundError`.
- Assuming the output is a NumPy array; the function returns a standard Python `list` of lists.

### Fix Code Hint
```python
# Ensure the file exists and is in the correct MIREX 2013 format
import os
if os.path.exists(filename):
    patterns = mir_eval.io.load_patterns(filename)
else:
    raise FileNotFoundError(f"Pattern file not found: {filename}")
```

## API Test: `load_ragged_time_series`

### Signature
```python
def load_ragged_time_series(filename, dtype=float, delimiter='\\s+', header=False, comment='#')
```
_Source: source/mir_eval/io.py:594_

_Source doc:_ Load data from a delimited time series annotation file with a variable number of columns. This function assumes that column 0 contains time stamps and columns 1 through n contain values. n may be variable from time stamp to time stamp. Examples -------- >>> # Load a ragged list of tab-delimited multi-f0 midi notes >>> times, vals = load_ragged_time_series('multif0.txt', dtype=int, delimiter='\t') >>> # Load a raggled list of space delimited multi-f0 values with a header >>> times, vals = load_ragged_time_series('labeled_events.csv', header=True) Parameters ---------- filename : str or `os.Pathlike` Path to the annotation file dtype : function Data type to apply to values columns. delimiter : str Separator regular expression. By default, lines will be split by any amount of whitespace. header : bool Indicates whether a header row is present or not. By default, assumes no header is present. comment : str or None Comment regular expression. Any lines beginning with this string or pattern will be ignored. Setting to `None` disables comments. Returns ------- times : np.ndarray array of timestamps (float) values : list of np.ndarray list of arrays of corresponding values

### Goal
Loads time-stamped annotation data from delimited text files where each row contains a timestamp followed by a variable number of associated values, commonly used for multi-pitch or multi-event transcription data.

### Parameters
- `filename`: Path to the annotation file (str or `os.PathLike`).
- `dtype`, default `float`: The data type (e.g., `int`, `float`) to apply to the values columns.
- `delimiter`, default `'\\s+'`: A regular expression string used to separate columns; defaults to any amount of whitespace.
- `header`, default `False`: Boolean flag; if `True`, the first row of the file is skipped.
- `comment`, default `'#'`: A string or pattern; lines starting with this are ignored. Set to `None` to disable.

### Input
The function expects a text file where the first column contains time stamps (floats) and subsequent columns contain values. The number of values per row can vary. The file must be accessible via the provided path.

### Output
Returns a tuple containing:
- `times`: A `np.ndarray` of timestamps (floats).
- `values`: A `list` of `np.ndarray` objects, where each array contains the values corresponding to the timestamp at the same index.

### Valid Call Patterns
```python
# Standard usage for multi-pitch data
times, pitches = mir_eval.io.load_ragged_time_series("data/multipitch/est01.txt")

# Usage with specific delimiter and data type
times, vals = mir_eval.io.load_ragged_time_series('multif0.txt', dtype=int, delimiter='\t')

# Usage with header row
times, vals = mir_eval.io.load_ragged_time_series('labeled_events.csv', header=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.io.load_ragged_time_series` as the fully qualified name.
- Ensure the `delimiter` is provided as a raw string if using backslashes (e.g., `r'\t'`).
- If the input file contains a header row, explicitly set `header=True` to avoid parsing errors.
- Use this function specifically for data where the number of columns varies per row; for fixed-column data, prefer other `mir_eval.io` loaders.

### Prompt Snippet
```text
from mir_eval.io import load_ragged_time_series
times, values = load_ragged_time_series(filename, dtype=float, delimiter=r'\s+', header=False, comment='#')
```

### Common Failure Modes
- **Delimiter Mismatch:** Providing a delimiter that does not match the file structure (e.g., using default whitespace for a comma-separated file) will result in parsing errors or empty value arrays.
- **Header Inclusion:** Failing to set `header=True` when a header exists will cause the first row of text to be interpreted as data, likely raising a `ValueError` during type conversion.
- **Path Errors:** Providing a non-existent file path will raise a `FileNotFoundError`.
- **Inconsistent Dtype:** Attempting to load non-numeric data with `dtype=int` will raise a `ValueError`.

### Fix Code Hint
```python
# If parsing fails due to unexpected delimiters, verify the file format:
import numpy as np
# Ensure the file is readable and check the delimiter
try:
    times, vals = mir_eval.io.load_ragged_time_series(filename, delimiter=',')
except ValueError as e:
    print(f"Check file format and delimiter: {e}")
```

## API Test: `load_tempo`

### Signature
```python
def load_tempo(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:542_

### Goal
Loads tempo annotations from a text-based file, returning the detected tempo values and their associated weights.

### Parameters
- `filename`: The path to the annotation file (supports `str`, `pathlib.Path`, or `os.PathLike`).
- `delimiter`, default `'\\s+'`: The regular expression pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file where each line contains tempo information. The file should be formatted such that the parser can extract numerical values for tempo and weight based on the provided `delimiter`. Preconditions include ensuring the file exists and is accessible, and that the file content conforms to the expected structure for tempo annotations.

### Output
Returns a tuple containing:
- `tempi`: A NumPy array of tempo values (floats).
- `weight`: A float representing the weight associated with the tempo annotations.

### Valid Call Patterns
```python
import mir_eval
# Standard usage with default delimiter and comment character
tempi, weight = mir_eval.io.load_tempo("data/tempo/ref01.lab")
```

### LLM Instruction Prompt
- When calling `load_tempo`, always provide the `filename` as the first argument.
- If the input file uses a non-standard delimiter or comment character, explicitly pass the `delimiter` and `comment` arguments.
- Ensure the returned `tempi` is treated as a NumPy array and `weight` as a float.

### Prompt Snippet
```text
tempi, weight = mir_eval.io.load_tempo(filename)
```

### Common Failure Modes
- **FileNotFoundError**: Occurs if the provided `filename` path is incorrect or the file does not exist.
- **Parsing Errors**: Occurs if the file content does not match the expected structure or if the `delimiter` does not correctly separate the tempo and weight columns.
- **Type Errors**: Passing an object that is not a valid path-like object to `filename`.

### Fix Code Hint
```python
import mir_eval
import os

# Ensure the file path is valid before loading
file_path = "path/to/tempo.lab"
if os.path.exists(file_path):
    tempi, weight = mir_eval.io.load_tempo(file_path)
else:
    print(f"File not found: {file_path}")
```

## API Test: `load_time_series`

### Signature
```python
def load_time_series(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:293_

### Goal
Loads time-series data (typically time and frequency pairs) from a delimited text file for use in MIR evaluation tasks such as melody transcription.

### Parameters
- `filename`: The path to the file to be loaded; accepts strings, open file descriptors, or `pathlib.Path` / `os.PathLike` objects.
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string prefix that denotes lines to be ignored as comments.

### Input
The function expects a text file where each row contains at least two columns: the first representing time stamps and the second representing corresponding values (e.g., frequency). The file must be formatted such that the specified `delimiter` correctly separates these columns.

### Output
Returns a tuple of two NumPy arrays: the first array contains the time stamps, and the second array contains the associated values.

### Valid Call Patterns
```python
import mir_eval
# Loading melody time-series data
ref_time, ref_freq = mir_eval.io.load_time_series('reference_melody.txt')
```

### LLM Instruction Prompt
- Use `mir_eval.io.load_time_series` when loading melody or pitch contour annotations.
- Always unpack the result into two variables (time and frequency/value arrays).
- Ensure the input file path is valid and accessible in the current environment.

### Prompt Snippet
```text
ref_time, ref_freq = mir_eval.io.load_time_series(filename)
```

### Common Failure Modes
- **Delimiter Mismatch:** Providing a file with a comma-separated format while using the default whitespace delimiter will cause the parser to fail to split columns correctly.
- **Malformed Files:** Files containing non-numeric data in the expected columns will raise a `ValueError` during array conversion.
- **Missing Files:** Passing a non-existent file path will raise an `IOError` or `FileNotFoundError`.

### Fix Code Hint
```python
# Ensure the delimiter matches the file format (e.g., use ',' for CSV)
data = mir_eval.io.load_time_series('data.csv', delimiter=',')
```

## API Test: `load_valued_intervals`

### Signature
```python
def load_valued_intervals(filename, delimiter='\\s+', comment='#')
```
_Source: source/mir_eval/io.py:448_

### Goal
Loads time-interval data with associated values (such as pitch or frequency) from a delimited text file for use in MIR evaluation tasks.

### Parameters
- `filename`: The path to the file containing the valued intervals. Supports `pathlib.Path` or `os.PathLike` objects.
- `delimiter`, default `'\\s+'`: The string or regex pattern used to separate columns in the input file.
- `comment`, default `'#'`: The character or string used to denote comment lines that should be ignored during parsing.

### Input
The function expects a text file where each row represents an interval and its associated value. Typically, this follows a format of `start_time`, `end_time`, and `value`. The input must be a valid path to a file accessible by the runtime.

### Output
Returns `unspecified` — The function returns a tuple containing two NumPy arrays: the first array contains the interval boundaries (start and end times), and the second array contains the corresponding values (e.g., frequencies or MIDI note numbers).

### Valid Call Patterns
```python
import mir_eval
# Loading transcription data for evaluation or display
intervals, values = mir_eval.io.load_valued_intervals("data/transcription/ref04.txt")
```

### LLM Instruction Prompt
- When calling `load_valued_intervals`, ensure the file path is provided as a string or path-like object.
- Always unpack the return value into two variables: one for the intervals (time array) and one for the associated values (data array).
- Use this function specifically for tasks involving valued intervals, such as multipitch transcription or melody evaluation.

### Prompt Snippet
```text
# Example of loading valued intervals for evaluation
intervals, values = mir_eval.io.load_valued_intervals(filename)
```

### Common Failure Modes
- **File Not Found:** Providing a path to a non-existent file will raise an `IOError` or `FileNotFoundError`.
- **Malformed Data:** If the text file does not contain the expected number of columns (e.g., missing values for an interval), the parser may raise a `ValueError` or return an incorrectly shaped array.
- **Delimiter Mismatch:** If the file uses a specific separator (like a comma) but the default `delimiter` (`\s+`) is used, the function will fail to parse the columns correctly.

### Fix Code Hint
```python
# Ensure the file exists and the delimiter matches the file format
import os
if os.path.exists(filename):
    intervals, values = mir_eval.io.load_valued_intervals(filename, delimiter=',')
```

## API Test: `load_wav`

### Signature
```python
def load_wav(path, mono=True)
```
_Source: source/mir_eval/io.py:409_

_Source doc:_ Load a .wav file as a numpy array using ``scipy.io.wavfile``. .. warning:: This function is deprecatred in mir_eval 0.8.1 and will be removed in 0.9.0. We recommend using a dedicated audio IO library such as `soundfile` instead. Parameters ---------- path : str or `os.Pathlike` Path to a .wav file mono : bool If the provided .wav has more than one channel, it will be converted to mono if ``mono=True``. (Default value = True) Returns ------- audio_data : np.ndarray Array of audio samples, normalized to the range [-1., 1.] fs : int Sampling rate of the audio data

### Goal
Loads a .wav audio file into a normalized NumPy array and retrieves its sampling rate for use in MIR evaluation tasks.

### Parameters
- `path`: The file system path to the .wav file, accepted as a `str` or `os.PathLike` object.
- `mono`, default `True`: A boolean flag; if `True`, multi-channel audio is downmixed to a single mono channel.

### Input
The function requires a valid path to a .wav file. Precondition: The file must be readable by `scipy.io.wavfile`.

### Output
Returns a tuple containing:
- `audio_data` (`np.ndarray`): The audio samples, normalized to the range [-1., 1.].
- `fs` (`int`): The sampling rate of the audio data.

### Valid Call Patterns
```python
# Standard usage for loading audio for evaluation
x, fs = load_wav("path/to/audio.wav")

# Loading with explicit mono conversion
x, fs = load_wav("path/to/audio.wav", mono=True)
```

### LLM Instruction Prompt
- Use `load_wav` only for legacy support or existing codebases; prefer `soundfile` for new implementations as `load_wav` is deprecated and scheduled for removal in `mir_eval` 0.9.0.
- Always unpack the return value into two variables: the audio array and the sampling rate.

### Prompt Snippet
```text
from mir_eval.io import load_wav
audio, fs = load_wav(file_path)
```

### Common Failure Modes
- **Deprecation:** Using this function in new projects will lead to compatibility issues in future versions of `mir_eval` (0.9.0+).
- **File Format:** Passing non-.wav files or corrupted audio files will raise errors from `scipy.io.wavfile`.
- **Missing Dependencies:** Requires `scipy` to be installed in the environment.

### Fix Code Hint
```python
# Recommended replacement for future-proofing
import soundfile as sf
audio, fs = sf.read(path, always_2d=True)
# If mono is required:
if mono and audio.shape[1] > 1:
    audio = audio.mean(axis=1)
```

## API Test: `majmin`

### Signature
```python
def majmin(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1107_

_Source doc:_ Compare chords along major-minor rules. Chords with qualities outside Major/minor/no-chord are ignored. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.majmin(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0], or -1 if the comparison is out of gamut.

### Goal
Evaluates chord estimation accuracy by comparing reference and estimated labels specifically against major and minor chord qualities, ignoring more complex chord types.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord annotations.
- `estimated_labels`: A list of strings representing the chord annotations produced by an estimation algorithm.

### Input
- **Data:** Both inputs must be lists of equal length `n` containing chord labels.
- **Preconditions:** Before calling `majmin`, inputs should be synchronized using `mir_eval.util.merge_labeled_intervals` to ensure that the reference and estimated labels correspond to the same time intervals.
- **Format:** Labels should follow standard MIR chord notation. Chords with qualities other than Major, minor, or no-chord are treated as out-of-gamut.

### Output
Returns a `np.ndarray` of shape `(n,)` and type `float`. Each element is a score in the range `[0.0, 1.0]` representing the match quality, or `-1` if the comparison is out of gamut (i.e., the chord quality is not Major, minor, or no-chord).

### Valid Call Patterns
```python
# Standard usage pattern as defined in the source documentation
comparisons = mir_eval.chord.majmin(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `majmin`, ensure that `reference_labels` and `estimated_labels` have been pre-processed to have identical temporal alignment using `mir_eval.util.merge_labeled_intervals`.
- Always interpret the returned `-1` values as "not applicable" or "out of gamut" rather than a zero-accuracy score.
- Use the resulting array to compute a weighted accuracy score via `mir_eval.chord.weighted_accuracy` if duration-based evaluation is required.

### Prompt Snippet
```text
# Ensure labels are aligned before calling majmin
intervals, ref_labels, est_labels = mir_eval.util.merge_labeled_intervals(ref_int, ref_lab, est_int, est_lab)
comparisons = mir_eval.chord.majmin(ref_labels, est_labels)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will cause the function to fail; ensure `merge_labeled_intervals` is used to align them first.
- **Ignoring Out-of-Gamut:** Users may mistakenly treat the `-1` return value as a low accuracy score; it must be filtered or handled specifically by the downstream metric (e.g., `weighted_accuracy` handles this).
- **Invalid Label Format:** Providing labels that do not conform to standard chord notation may result in unexpected behavior or incorrect classification of "out of gamut" chords.

### Fix Code Hint
```python
# If comparisons contain -1, filter them before calculating mean accuracy
valid_mask = comparisons != -1
score = np.mean(comparisons[valid_mask]) if np.any(valid_mask) else 0.0
```

## API Test: `majmin_inv`

### Signature
```python
def majmin_inv(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1172_

_Source doc:_ Compare chords along major-minor rules, with inversions. Chords with qualities outside Major/minor/no-chord are ignored, and the bass note must exist in the triad (bass in [1, 3, 5]). Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.majmin_inv(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0], or -1 if the comparison is out of gamut.

### Goal
Evaluates chord estimation accuracy by comparing reference and estimated labels based on major/minor triad membership and bass note inversion, ignoring qualities outside the Major/minor/no-chord gamut.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord labels, of length `n`.
- `estimated_labels`: A list of strings representing the estimated chord labels, of length `n`.

### Input
- **Data:** Requires two lists of chord labels of equal length.
- **Preconditions:** Labels must be formatted as standard MIR chord strings. Before calling, use `mir_eval.util.merge_labeled_intervals` to ensure reference and estimated labels are aligned over the same time intervals.
- **Gamut:** Chords with qualities other than Major, minor, or no-chord are ignored. The bass note must be a member of the triad (1, 3, or 5).

### Output
Returns a `np.ndarray` of shape `(n,)` and `dtype=float`. Each element is a score in the range `[0.0, 1.0]`, or `-1` if the comparison falls outside the supported gamut.

### Valid Call Patterns
```python
# Standard usage after interval merging
comparisons = mir_eval.chord.majmin_inv(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `majmin_inv`, ensure that `reference_labels` and `estimated_labels` have been synchronized using `mir_eval.util.merge_labeled_intervals` so that each index `i` in the lists corresponds to the same time interval.
- Always check for `-1` values in the output array, as these indicate labels that were ignored due to being outside the supported Major/minor/no-chord gamut.

### Prompt Snippet
```text
# Ensure labels are aligned before calling
comparisons = mir_eval.chord.majmin_inv(ref_labels, est_labels)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `len(reference_labels) == len(estimated_labels)` by using `mir_eval.util.merge_labeled_intervals`.
- **Unsupported Qualities:** Providing chord labels that are not Major, minor, or no-chord will result in a score of `-1` for those indices.
- **Invalid Bass Notes:** If the bass note is not part of the triad (1, 3, 5), the comparison will be considered out of gamut and return `-1`.

### Fix Code Hint
```python
# If you receive -1, verify your chord labels are in the supported Major/minor format
# and that the bass note is a valid triad member.
valid_mask = comparisons != -1
filtered_scores = comparisons[valid_mask]
```

## API Test: `match_events`

### Signature
```python
def match_events(ref, est, window, distance=None)
```
_Source: source/mir_eval/util.py:644_

_Source doc:_ Compute a maximum matching between reference and estimated event times, subject to a window constraint. Given two lists of event times ``ref`` and ``est``, we seek the largest set of correspondences ``(ref[i], est[j])`` such that ``distance(ref[i], est[j]) <= window``, and each ``ref[i]`` and ``est[j]`` is matched at most once. This is useful for computing precision/recall metrics in beat tracking, onset detection, and segmentation. Parameters ---------- ref : np.ndarray, shape=(n,) Array of reference values est : np.ndarray, shape=(m,) Array of estimated values window : float > 0 Size of the window. distance : function function that computes the outer distance of ref and est. By default uses ``|ref[i] - est[j]|`` Returns ------- matching : list of tuples A list of matched reference and event numbers. ``matching[i] == (i, j)`` where ``ref[i]`` matches ``est[j]``.

### Goal
Compute a maximum bipartite matching between reference and estimated event time arrays to facilitate precision and recall calculations in MIR tasks like beat tracking and onset detection.

### Parameters
- `ref`: `np.ndarray`, shape=(n,). The ground-truth event times.
- `est`: `np.ndarray`, shape=(m,). The estimated event times to be evaluated.
- `window`: `float > 0`. The maximum allowable distance between a reference and estimated event to consider them a match.
- `distance`, default `None`: `function`. A custom function to compute the distance matrix between `ref` and `est`. If `None`, defaults to the absolute difference `|ref[i] - est[j]|`.

### Input
The function expects `ref` and `est` to be one-dimensional NumPy arrays containing numerical event times (e.g., in seconds). Preconditions: The arrays should be sorted chronologically for standard distance metrics. If using custom distance functions (like modular arithmetic for beat tracking), ensure the function signature is compatible with the expected outer distance computation.

### Output
Returns `list of tuples`. Each tuple `(i, j)` represents a match where the reference event at index `i` is associated with the estimated event at index `j`.

### Valid Call Patterns
```python
import mir_eval
# Standard matching with default distance
matches = mir_eval.util.match_events(ref, est, 0.5)

# Matching with custom distance function (e.g., modular distance)
matches = mir_eval.util.match_events(
    ref, est, 0.5, distance=mir_eval.util._outer_distance_mod_n
)
```

### LLM Instruction Prompt
- Always use `mir_eval.util.match_events` as the fully qualified name.
- Ensure `window` is a positive float.
- If the task involves periodic events (like beats), consider passing a custom `distance` function to handle wrap-around logic.
- The output is a list of index pairs; use these to index into the original `ref` and `est` arrays to calculate performance metrics.

### Prompt Snippet
```text
# Example usage for onset detection evaluation:
matches = mir_eval.util.match_events(reference_onsets, estimated_onsets, window=0.05)
precision = len(matches) / len(estimated_onsets)
recall = len(matches) / len(reference_onsets)
```

### Common Failure Modes
- **Window Size:** Setting `window` too small may result in zero matches, while setting it too large may result in incorrect, non-causal matches.
- **Unsorted Inputs:** If the `distance` function assumes chronological order, passing unsorted arrays may lead to unexpected matching behavior.
- **Type Mismatch:** Passing lists instead of `np.ndarray` may cause errors if the internal distance calculation relies on NumPy broadcasting.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays and sorted
ref = np.sort(np.array(ref))
est = np.sort(np.array(est))
matches = mir_eval.util.match_events(ref, est, window=0.1)
```

## API Test: `match_note_offsets`

### Signature
```python
def match_note_offsets(ref_intervals, est_intervals, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False)
```
_Source: source/mir_eval/transcription.py:169_

_Source doc:_ Compute a maximum matching between reference and estimated notes, only taking note offsets into account. Given two note sequences represented by ``ref_intervals`` and ``est_intervals`` (see :func:`mir_eval.io.load_valued_intervals`), we seek the largest set of correspondences ``(i, j)`` such that the offset of reference note ``i`` has to be within ``offset_tolerance`` of the offset of estimated note ``j``, where ``offset_tolerance`` is equal to ``offset_ratio`` times the reference note's duration, i.e.  ``offset_ratio * ref_duration[i]`` where ``ref_duration[i] = ref_intervals[i, 1] - ref_intervals[i, 0]``. If the resulting ``offset_tolerance`` is less than ``offset_min_tolerance`` (50 ms by default) then ``offset_min_tolerance`` is used instead. Every reference note is matched against at most one estimated note. Note there are separate functions :func:`match_note_onsets` and :func:`match_notes` for matching notes based on onsets only or based on onset, offset, and pitch, respectively. This is because the rules for matching note onsets and matching note offsets are different. Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) offset_ratio : float > 0 The ratio of the reference note's duration used to define the ``offset_tolerance``. Default is 0.2 (20%), meaning the ``offset_tolerance`` will equal the ``ref_duration * 0.2``, or 0.05 (50 ms), whichever is greater. offset_min_tolerance : float > 0 The minimum tolerance for offset matching. See ``offset_ratio`` description for an explanation of how the offset tolerance is determined. strict : bool If ``strict=False`` (the default), threshold checks for offset matching are performed using ``<=`` (less than or equal). If ``strict=True``, the threshold checks are performed using ``<`` (less than). Returns ------- matching : list of tuples A list of matched reference and estimated notes. ``matching[i] == (i, j)`` where reference note ``i`` matches estimated note ``j``.

### Goal
Identify the largest set of correspondences between reference and estimated note sequences based exclusively on the temporal alignment of note offsets.

### Parameters
- `ref_intervals`: `np.ndarray`, shape=(n,2). Array of reference note time intervals (onset and offset times).
- `est_intervals`: `np.ndarray`, shape=(m,2). Array of estimated note time intervals (onset and offset times).
- `offset_ratio`, default `0.2`: `float > 0`. The ratio of the reference note's duration used to define the tolerance window.
- `offset_min_tolerance`, default `0.05`: `float > 0`. The minimum absolute tolerance (in seconds) for offset matching.
- `strict`, default `False`: `bool`. If `False`, uses `<=` for threshold comparison; if `True`, uses `<`.

### Input
Requires two `np.ndarray` objects containing note intervals. These are typically loaded via `mir_eval.io.load_valued_intervals`. Precondition: The arrays must be structured as `(n, 2)` where column 0 is the onset time and column 1 is the offset time.

### Output
Returns `list of tuples`. Each tuple `(i, j)` represents a match where reference note index `i` corresponds to estimated note index `j`.

### Valid Call Patterns
```python
import mir_eval
# Standard matching
matching = mir_eval.transcription.match_note_offsets(ref_int, est_int)

# Strict matching
matching = mir_eval.transcription.match_note_offsets(ref_int, est_int, strict=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.transcription.match_note_offsets` as the fully qualified name.
- Ensure `ref_intervals` and `est_intervals` are NumPy arrays of shape `(n, 2)`.
- Remember that this function ignores pitch and onset times, focusing solely on the offset alignment relative to note duration.

### Prompt Snippet
```text
mir_eval.transcription.match_note_offsets(ref_intervals, est_intervals, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False)
```

### Common Failure Modes
- Passing arrays with incorrect shapes (e.g., missing the offset column or including pitch/velocity columns).
- Assuming this function accounts for pitch; it does not, which may lead to "matches" between notes of different pitches if their offsets align.
- Providing `offset_ratio` or `offset_min_tolerance` values that are not positive, which violates the parameter constraints.

### Fix Code Hint
```python
# Ensure input arrays only contain onset and offset columns
ref_int = ref_data[:, :2]
est_int = est_data[:, :2]
matching = mir_eval.transcription.match_note_offsets(ref_int, est_int)
```

## API Test: `match_note_onsets`

### Signature
```python
def match_note_onsets(ref_intervals, est_intervals, onset_tolerance=0.05, strict=False)
```
_Source: source/mir_eval/transcription.py:262_

_Source doc:_ Compute a maximum matching between reference and estimated notes, only taking note onsets into account. Given two note sequences represented by ``ref_intervals`` and ``est_intervals`` (see :func:`mir_eval.io.load_valued_intervals`), we see the largest set of correspondences ``(i,j)`` such that the onset of reference note ``i`` is within ``onset_tolerance`` of the onset of estimated note ``j``. Every reference note is matched against at most one estimated note. Note there are separate functions :func:`match_note_offsets` and :func:`match_notes` for matching notes based on offsets only or based on onset, offset, and pitch, respectively. This is because the rules for matching note onsets and matching note offsets are different. Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) onset_tolerance : float > 0 The tolerance for an estimated note's onset deviating from the reference note's onset, in seconds. Default is 0.05 (50 ms). strict : bool If ``strict=False`` (the default), threshold checks for onset matching are performed using ``<=`` (less than or equal). If ``strict=True``, the threshold checks are performed using ``<`` (less than). Returns ------- matching : list of tuples A list of matched reference and estimated notes. ``matching[i] == (i, j)`` where reference note ``i`` matches estimated note ``j``.

### Goal
Identifies the maximum set of correspondences between reference and estimated note sequences based solely on the temporal proximity of their onsets.

### Parameters
- `ref_intervals`: A NumPy array of shape `(n, 2)` containing reference note intervals (onset and offset times in seconds).
- `est_intervals`: A NumPy array of shape `(m, 2)` containing estimated note intervals (onset and offset times in seconds).
- `onset_tolerance`, default `0.05`: A float representing the maximum allowed deviation in seconds between a reference onset and an estimated onset.
- `strict`, default `False`: A boolean flag; if `True`, uses a strict inequality (`<`) for the tolerance check; if `False`, uses a non-strict inequality (`<=`).

### Input
Requires two NumPy arrays of shape `(n, 2)` and `(m, 2)`. These are typically loaded using `mir_eval.io.load_valued_intervals`. Ensure that the input arrays contain valid time intervals where the first column represents the onset time.

### Output
Returns a `list` of `tuples`. Each tuple `(i, j)` indicates that the reference note at index `i` is matched with the estimated note at index `j`.

### Valid Call Patterns
```python
import mir_eval
# Standard usage
matching = mir_eval.transcription.match_note_onsets(ref_int, est_int)

# Usage with strict threshold
matching = mir_eval.transcription.match_note_onsets(ref_int, est_int, strict=True)
```

### LLM Instruction Prompt
- Always use the `mir_eval.transcription` namespace when calling this function.
- Ensure input arrays are NumPy arrays of shape `(n, 2)`.
- Do not confuse this with `match_notes` (which considers pitch and offsets) or `match_note_offsets`.

### Prompt Snippet
```text
from mir_eval.transcription import match_note_onsets
# matching = match_note_onsets(ref_intervals, est_intervals, onset_tolerance=0.05, strict=False)
```

### Common Failure Modes
- Passing arrays with incorrect shapes (e.g., missing the offset column or having extra dimensions).
- Assuming this function evaluates pitch or offset accuracy; it only evaluates onset timing.
- Providing `onset_tolerance` values that are non-positive, which may lead to unexpected matching behavior.

### Fix Code Hint
```python
# Ensure inputs are properly formatted as (n, 2) arrays
import numpy as np
ref_intervals = np.array(ref_data) # Ensure shape is (n, 2)
est_intervals = np.array(est_data) # Ensure shape is (m, 2)
matching = mir_eval.transcription.match_note_onsets(ref_intervals, est_intervals)
```

## API Test: `match_notes`

### Signature
```python
def match_notes(ref_intervals, ref_pitches, ref_velocities, est_intervals, est_pitches, est_velocities, onset_tolerance=0.05, pitch_tolerance=50.0, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False, velocity_tolerance=0.1)
def match_notes(ref_intervals, ref_pitches, est_intervals, est_pitches, onset_tolerance=0.05, pitch_tolerance=50.0, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False)
```
_Source: source/mir_eval/transcription_velocity.py:107  (+1 more definition site/overload)_

_Source doc:_ Match notes, taking note velocity into consideration. This function first calls :func:`mir_eval.transcription.match_notes` to match notes according to the supplied intervals, pitches, onset, offset, and pitch tolerances. The velocities of the matched notes are then used to estimate a slope and intercept which can rescale the estimated velocities so that they are as close as possible (in L2 sense) to their matched reference velocities. Velocities are then normalized to the range [0, 1]. A estimated note is then further only considered correct if its velocity is within ``velocity_tolerance`` of its matched (according to pitch and timing) reference note.

### Goal
Matches estimated notes to reference notes based on timing, pitch, and velocity criteria to evaluate transcription performance.

### Parameters
- `ref_intervals`: `np.ndarray`, shape=(n,2). Array of reference note time intervals (onset and offset times).
- `ref_pitches`: `np.ndarray`, shape=(n,). Array of reference pitch values in Hertz.
- `ref_velocities`: `np.ndarray`, shape=(n,). Array of MIDI velocities (0-127) of reference notes.
- `est_intervals`: `np.ndarray`, shape=(m,2). Array of estimated note time intervals (onset and offset times).
- `est_pitches`: `np.ndarray`, shape=(m,). Array of estimated pitch values in Hertz.
- `est_velocities`: `np.ndarray`, shape=(m,). Array of MIDI velocities (0-127) of estimated notes.
- `onset_tolerance`, default `0.05`: Tolerance for onset deviation in seconds.
- `pitch_tolerance`, default `50.0`: Tolerance for pitch deviation in cents.
- `offset_ratio`, default `0.2`: Ratio of reference duration used to define offset tolerance; if `None`, offsets are ignored.
- `offset_min_tolerance`, default `0.05`: Minimum tolerance for offset matching in seconds.
- `strict`, default `False`: If `False`, uses `<=` for threshold checks; if `True`, uses `<`.
- `velocity_tolerance`, default `0.1`: Tolerance for velocity deviation after rescaling and normalization to [0, 1].

### Input
Requires NumPy arrays for all interval, pitch, and velocity inputs. Reference and estimated arrays must be aligned by index (n reference notes, m estimated notes). Precondition: Ensure input arrays are formatted as expected by `mir_eval` transcription tasks (e.g., intervals as `[onset, offset]` pairs).

### Output
Returns `matching`: A list of tuples `(i, j)` where reference note `i` matches estimated note `j`.

### Valid Call Patterns
```python
# Example inferred from signature and project test patterns
import mir_eval
import numpy as np

# Assuming ref_int, ref_pitch, ref_vel, est_int, est_pitch, est_vel are defined
matching = mir_eval.transcription_velocity.match_notes(
    ref_int, ref_pitch, ref_vel, est_int, est_pitch, est_vel
)
```

### LLM Instruction Prompt
- Always use `mir_eval.transcription_velocity.match_notes` when velocity matching is required.
- Ensure `ref_velocities` and `est_velocities` are provided as NumPy arrays of MIDI values (0-127).
- If velocity information is unavailable, use `mir_eval.transcription.match_notes` instead.

### Prompt Snippet
```text
from mir_eval.transcription_velocity import match_notes
# match_notes(ref_intervals, ref_pitches, ref_velocities, est_intervals, est_pitches, est_velocities, ...)
```

### Common Failure Modes
- Passing lists instead of NumPy arrays, which may cause indexing or broadcasting errors.
- Mismatched array lengths between intervals, pitches, and velocities for the same set (reference or estimated).
- Setting `offset_ratio=None` when strict offset timing is required for the evaluation.
- Providing velocity values outside the 0-127 MIDI range, which may lead to unexpected normalization results.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays
ref_velocities = np.array(ref_velocities)
est_velocities = np.array(est_velocities)
# Call the function
matches = mir_eval.transcription_velocity.match_notes(
    ref_int, ref_pitch, ref_velocities, est_int, est_pitch, est_velocities
)
```

## API Test: `merge_chord_intervals`

### Signature
```python
def merge_chord_intervals(intervals, labels)
```
_Source: source/mir_eval/chord.py:1490_

_Source doc:_ Merge consecutive chord intervals if they represent the same chord. Parameters ---------- intervals : np.ndarray, shape=(n, 2), dtype=float Chord intervals to be merged, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. labels : list, shape=(n,) Chord labels to be merged, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. Returns ------- merged_ivs : np.ndarray, shape=(k, 2), dtype=float Merged chord intervals, k <= n

### Goal
Reduces chord annotation complexity by merging adjacent time intervals that share identical chord labels into a single continuous interval.

### Parameters
- `intervals`: A NumPy array of shape `(n, 2)` containing start and end times for `n` chord segments, typically obtained via `mir_eval.io.load_labeled_intervals`.
- `labels`: A list of length `n` containing the string representations of the chords corresponding to each interval.

### Input
The function expects `intervals` as a `np.ndarray` of floats and `labels` as a list of strings. Both inputs must be aligned such that `labels[i]` corresponds to `intervals[i]`. These are typically prepared using `mir_eval.io.load_labeled_intervals`.

### Output
Returns a `np.ndarray` of shape `(k, 2)` where `k <= n`, representing the merged time intervals.

### Valid Call Patterns
```python
import numpy as np
import mir_eval

intervals = np.array([[0.0, 1.0], [1.0, 2.0], [2.0, 3.0]])
labels = ["C:maj", "C:maj", "A:min"]
merged = mir_eval.chord.merge_chord_intervals(intervals, labels)
```

### LLM Instruction Prompt
- When calling `merge_chord_intervals`, ensure the `intervals` array and `labels` list are of equal length `n`.
- The function only merges *consecutive* intervals; if identical labels are separated by a different label, they will not be merged.
- Always use the `mir_eval.chord` namespace for this function.

### Prompt Snippet
```text
# Merge consecutive identical chord labels
merged_intervals = mir_eval.chord.merge_chord_intervals(intervals, labels)
```

### Common Failure Modes
- **Mismatched Lengths:** Providing an `intervals` array and `labels` list of different lengths will result in an index error or invalid processing.
- **Non-Consecutive Identical Labels:** If the input contains `['C', 'A', 'C']`, the function will not merge the two 'C' intervals because they are not consecutive.
- **Data Type Errors:** Passing non-numeric types in the `intervals` array will cause NumPy-related failures.

### Fix Code Hint
```python
# Ensure inputs are aligned and consecutive segments are identified
import numpy as np
# If labels are not consecutive, consider sorting or re-ordering if the task allows
# Otherwise, ensure the input arrays are pre-validated for length
if len(intervals) != len(labels):
    raise ValueError("Intervals and labels must have the same length.")
```

## API Test: `merge_labeled_intervals`

### Signature
```python
def merge_labeled_intervals(x_intervals, x_labels, y_intervals, y_labels)
```
_Source: source/mir_eval/util.py:481_

_Source doc:_ Merge the time intervals of two sequences. Parameters ---------- x_intervals : np.ndarray Array of interval times (seconds) x_labels : list or None List of labels y_intervals : np.ndarray Array of interval times (seconds) y_labels : list or None List of labels Returns ------- new_intervals : np.ndarray New interval times of the merged sequences. new_x_labels : list New labels for the sequence ``x`` new_y_labels : list New labels for the sequence ``y``

### Goal
Merges two sets of labeled time intervals into a single, unified timeline by calculating the intersection of all interval boundaries, allowing for the comparison or alignment of two distinct annotation sequences.

### Parameters
- `x_intervals`: A `np.ndarray` of shape `(n, 2)` representing the start and end times (in seconds) of the first sequence.
- `x_labels`: A `list` of labels corresponding to each interval in `x_intervals`.
- `y_intervals`: A `np.ndarray` of shape `(m, 2)` representing the start and end times (in seconds) of the second sequence.
- `y_labels`: A `list` of labels corresponding to each interval in `y_intervals`.

### Input
The function expects two sets of labeled intervals. Preconditions:
- Both `x_intervals` and `y_intervals` must be valid `np.ndarray` objects.
- The sequences must cover the same total duration; if the end time of the last interval in `x_intervals` does not match the end time of the last interval in `y_intervals`, a `ValueError` will be raised.
- Labels should be provided as lists of equal length to their respective interval arrays.

### Output
Returns a tuple containing:
- `new_intervals`: A `np.ndarray` of the merged time boundaries.
- `new_x_labels`: A `list` of labels for the first sequence, expanded to match the new merged intervals.
- `new_y_labels`: A `list` of labels for the second sequence, expanded to match the new merged intervals.

### Valid Call Patterns
```python
from mir_eval import util
import numpy as np

new_intvs, new_x_labels, new_y_labels = util.merge_labeled_intervals(
    x_intvs, x_labels, y_intvs, y_labels
)
```

### LLM Instruction Prompt
- Always import `util` from `mir_eval` to access this function.
- Ensure `x_intervals` and `y_intervals` are `numpy.ndarray` objects before calling.
- The function returns three values; ensure all are captured in the assignment.
- Use this function when you need to perform point-wise or segment-wise comparison between two different annotation sources that have different boundary timings.

### Prompt Snippet
```text
# Example of merging two interval sequences
new_intervals, x_labs, y_labs = mir_eval.util.merge_labeled_intervals(
    x_intervals, x_labels, y_intervals, y_labels
)
```

### Common Failure Modes
- **`ValueError`**: Raised if the total duration of the two interval sequences does not match (i.e., the end time of the last interval in `x` differs from the end time of the last interval in `y`).
- **Type Mismatch**: Passing standard Python lists instead of `np.ndarray` for the interval arguments may cause internal indexing errors.
- **Empty Inputs**: Providing empty arrays or lists may result in unexpected behavior or errors depending on the downstream processing of the returned empty structures.

### Fix Code Hint
```python
# Ensure intervals are numpy arrays and durations match
x_intervals = np.array(x_intervals)
y_intervals = np.array(y_intervals)

if x_intervals[-1, 1] != y_intervals[-1, 1]:
    # Handle or log duration mismatch before calling merge_labeled_intervals
    pass
```

## API Test: `metrics`

### Signature
```python
def metrics(ref_time, ref_freqs, est_time, est_freqs, **kwargs)
```
_Source: source/mir_eval/multipitch.py:348_

_Source doc:_ Compute multipitch metrics. All metrics are computed at the 'macro' level such that the frame true positive/false positive/false negative rates are summed across time and the metrics are computed on the combined values. Examples -------- >>> ref_time, ref_freqs = mir_eval.io.load_ragged_time_series( ...     'reference.txt') >>> est_time, est_freqs = mir_eval.io.load_ragged_time_series( ...     'estimated.txt') >>> metris_tuple = mir_eval.multipitch.metrics( ...     ref_time, ref_freqs, est_time, est_freqs) Parameters ---------- ref_time : np.ndarray Time of each reference frequency value ref_freqs : list of np.ndarray List of np.ndarrays of reference frequency values est_time : np.ndarray Time of each estimated frequency value est_freqs : list of np.ndarray List of np.ndarrays of estimate frequency values **kwargs Additional keyword arguments which will be passed to the appropriate metric or preprocessing functions. Returns ------- precision : float Precision (TP/(TP + FP)) recall : float Recall (TP/(TP + FN)) accuracy : float Accuracy (TP/(TP + FP + FN)) e_sub : float Substitution error e_miss : float Miss error e_fa : float False alarm error e_tot : float Total error precision_chroma : float Chroma precision recall_chroma : float Chroma recall accuracy_chroma : float Chroma accuracy e_sub_chroma : float Chroma substitution error e_miss_chroma : float Chroma miss error e_fa_chroma : float Chroma false alarm error e_tot_chroma : float Chroma total error

### Goal
Computes macro-averaged multipitch evaluation metrics by comparing reference and estimated frequency time series at the frame level.

### Parameters
- `ref_time`: `np.ndarray` containing the time stamps for each reference frequency observation.
- `ref_freqs`: `list` of `np.ndarray` objects representing the reference frequency values corresponding to each time stamp.
- `est_time`: `np.ndarray` containing the time stamps for each estimated frequency observation.
- `est_freqs`: `list` of `np.ndarray` objects representing the estimated frequency values corresponding to each time stamp.
- `**kwargs`: Additional keyword arguments passed to internal metric or preprocessing functions.

### Input
Data should be loaded using `mir_eval.io.load_ragged_time_series` or equivalent structures. Inputs must be aligned in time and frequency format. Preconditions include ensuring that the reference and estimated data are properly formatted as ragged time series (time arrays paired with lists of frequency arrays).

### Output
Returns a tuple of floats representing: `precision`, `recall`, `accuracy`, `e_sub`, `e_miss`, `e_fa`, `e_tot`, `precision_chroma`, `recall_chroma`, `accuracy_chroma`, `e_sub_chroma`, `e_miss_chroma`, `e_fa_chroma`, and `e_tot_chroma`.

### Valid Call Patterns
```python
import mir_eval
ref_time, ref_freqs = mir_eval.io.load_ragged_time_series('reference.txt')
est_time, est_freqs = mir_eval.io.load_ragged_time_series('estimated.txt')
metrics = mir_eval.multipitch.metrics(ref_time, ref_freqs, est_time, est_freqs)
```

### LLM Instruction Prompt
- Always use `mir_eval.multipitch.metrics` to compute multipitch evaluation.
- Ensure inputs are loaded via `mir_eval.io.load_ragged_time_series` before passing to the function.
- The function returns a fixed-length tuple of 14 float values; ensure the caller unpacks these correctly.

### Prompt Snippet
```text
# Example of calling multipitch metrics
ref_time, ref_freqs = mir_eval.io.load_ragged_time_series(ref_path)
est_time, est_freqs = mir_eval.io.load_ragged_time_series(est_path)
scores = mir_eval.multipitch.metrics(ref_time, ref_freqs, est_time, est_freqs)
```

### Common Failure Modes
- Mismatched lengths between `ref_time` and `ref_freqs` (or `est_time` and `est_freqs`), which will cause indexing errors during metric computation.
- Providing non-ragged arrays when the function expects a list of `np.ndarray` objects.
- Failure to handle empty input files, which may result in division by zero or empty metric calculations.

### Fix Code Hint
```python
# Ensure data is loaded correctly as ragged series
ref_time, ref_freqs = mir_eval.io.load_ragged_time_series(ref_path)
# Validate that arrays are not empty before processing
if len(ref_time) > 0:
    results = mir_eval.multipitch.metrics(ref_time, ref_freqs, est_time, est_freqs)
```

## API Test: `midi_to_chroma`

### Signature
```python
def midi_to_chroma(frequencies_midi)
```
_Source: source/mir_eval/multipitch.py:173_

_Source doc:_ Wrap MIDI frequencies to a single octave (chroma). Parameters ---------- frequencies_midi : list of np.ndarray Continuous MIDI note frequency values. Returns ------- frequencies_chroma : list of np.ndarray Midi values wrapped to one octave.

### Goal
Wraps continuous MIDI note frequency values into a single octave (chroma) representation, typically used for pitch-class analysis in multipitch estimation tasks.

### Parameters
- `frequencies_midi`: A list of `np.ndarray` objects, where each array contains continuous MIDI note frequency values to be wrapped.

### Input
- **Data:** The input must be a list of NumPy arrays containing floating-point MIDI note values.
- **Preconditions:** The input should represent MIDI note numbers (where 69.0 corresponds to A4). The function assumes the input is structured as a list of arrays, consistent with the format used in `mir_eval.multipitch` evaluation workflows.

### Output
Returns `list of np.ndarray` — A list of NumPy arrays where each MIDI value has been wrapped to the range [0, 12), representing the pitch class (chroma) of the input frequencies.

### Valid Call Patterns
```python
import numpy as np
import mir_eval

midi_frequencies = [np.array([69.0, 57.0, 76.01955000865388])]
chroma = mir_eval.multipitch.midi_to_chroma(midi_frequencies)
```

### LLM Instruction Prompt
- When calling `mir_eval.multipitch.midi_to_chroma`, ensure the input is a list of `np.ndarray` objects.
- Do not attempt to pass raw frequency values in Hertz; ensure they are converted to the MIDI note number scale first.
- The output will maintain the same list-of-arrays structure as the input.

### Prompt Snippet
```text
# Wrap MIDI frequencies to chroma
chroma_data = mir_eval.multipitch.midi_to_chroma(midi_data)
```

### Common Failure Modes
- **Type Mismatch:** Passing a single `np.ndarray` instead of a `list` of `np.ndarray` objects will cause the function to fail or produce unexpected indexing behavior.
- **Scale Error:** Passing frequencies in Hertz instead of MIDI note numbers will result in mathematically incorrect chroma values.
- **Empty Input:** While the function handles empty arrays within the list, ensure the input list itself is not `None`.

### Fix Code Hint
```python
# Ensure input is a list of arrays
if not isinstance(midi_data, list):
    midi_data = [np.array(midi_data)]
chroma = mir_eval.multipitch.midi_to_chroma(midi_data)
```

## API Test: `midi_to_hz`

### Signature
```python
def midi_to_hz(midi)
```
_Source: source/mir_eval/util.py:930_

_Source doc:_ Convert MIDI numbers to Hz Parameters ---------- midi : number or ndarray MIDI notes Returns ------- freqs : number or ndarray Frequency/frequencies in Hz corresponding to `midi`

### Goal
Converts MIDI note numbers into their corresponding frequencies in Hertz (Hz) to facilitate pitch-based evaluation and signal processing tasks.

### Parameters
- `midi`: A number or `numpy.ndarray` representing MIDI note numbers.

### Input
- **Data:** The input must be a numeric value or a NumPy array of numeric values representing MIDI notes.
- **Preconditions:** The input should be compatible with standard MIDI pitch definitions (e.g., 69 corresponds to A4 at 440 Hz).

### Output
Returns `number or ndarray` — The frequency or frequencies in Hz corresponding to the input MIDI note(s).

### Valid Call Patterns
```python
import mir_eval.util
# Example inferred from signature
freqs = mir_eval.util.midi_to_hz(69)
```

### LLM Instruction Prompt
- Use `mir_eval.util.midi_to_hz` when converting MIDI pitch representations to frequency values for evaluation metrics.
- Ensure the input is a numeric type or a NumPy array.
- Do not attempt to pass file paths or non-numeric objects to this function.

### Prompt Snippet
```text
# Convert MIDI note 60 to Hz
freq = mir_eval.util.midi_to_hz(60)
```

### Common Failure Modes
- **Type Errors:** Passing non-numeric types (e.g., strings or lists that cannot be cast to NumPy arrays) will result in errors during the internal calculation.
- **Input Validation:** While the function handles scalars and arrays, it expects valid MIDI note numbers; providing non-standard or extreme values may result in frequencies outside the audible range.

### Fix Code Hint
```python
import numpy as np
import mir_eval.util

# Ensure input is a numpy array if processing multiple notes
midi_notes = np.array([60, 62, 64])
freqs = mir_eval.util.midi_to_hz(midi_notes)
```

## API Test: `mirex`

### Signature
```python
def mirex(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1044_

_Source doc:_ Compare chords along MIREX rules. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.mirex(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Computes chord-wise comparison scores based on the MIREX (Music Information Retrieval Evaluation eXchange) evaluation rules for chord recognition.

### Parameters
- `reference_labels`: A list of ground-truth chord labels (strings) of length `n`.
- `estimated_labels`: A list of estimated chord labels (strings) of length `n`.

### Input
The function expects two lists of chord labels of equal length. Before calling `mirex`, inputs must be preprocessed using `mir_eval.util.merge_labeled_intervals` to ensure that the reference and estimated labels are aligned over the same time intervals.

### Output
Returns a `np.ndarray` of shape `(n,)` and type `float`, containing comparison scores for each interval in the range `[0.0, 1.0]`.

### Valid Call Patterns
```python
import mir_eval
# Assuming ref_labels and est_labels are aligned via merge_labeled_intervals
comparisons = mir_eval.chord.mirex(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `mirex`, ensure that the input lists are derived from `mir_eval.util.merge_labeled_intervals` to guarantee temporal alignment.
- The output of this function is intended to be passed to `mir_eval.chord.weighted_accuracy` along with the corresponding interval durations to compute the final MIREX score.

### Prompt Snippet
```text
comparisons = mir_eval.chord.mirex(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Providing `reference_labels` and `estimated_labels` of different lengths will result in an error.
- **Unmerged Intervals:** Passing raw labels without first using `mir_eval.util.merge_labeled_intervals` will lead to incorrect evaluation because the labels will not correspond to the same time segments.
- **Invalid Labels:** Using chord labels not recognized by the `mir_eval.chord` parser may cause unexpected behavior or errors during comparison.

### Fix Code Hint
```python
# Ensure intervals are merged before comparison
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.mirex(ref_labels, est_labels)
```

## API Test: `multipitch`

### Signature
```python
def multipitch(times, frequencies, midi=False, unvoiced=False, ax=None, prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:762_

_Source doc:_ Visualize multiple f0 measurements Parameters ---------- times : np.ndarray, shape=(n,) Sample times of frequencies frequencies : list of np.ndarray frequencies (in Hz) of the pitch measurements. Voicing is indicated by sign (positive for voiced, non-positive for non-voiced). `times` and `frequencies` should be in the format produced by :func:`mir_eval.io.load_ragged_time_series` midi : bool If `True`, plot on a MIDI-numbered vertical axis. Otherwise, plot on a linear frequency axis. unvoiced : bool If `True`, unvoiced pitches are plotted and indicated by transparency. Otherwise, unvoiced pitches are omitted from the display. ax : matplotlib.pyplot.axes An axis handle on which to draw the pitch contours. If none is provided, a new set of axes is created. prop_cycle : cycle.Cycler An optional property cycle object to specify style properties. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to `plt.scatter`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes multiple fundamental frequency (f0) contours over time to allow for qualitative inspection of multipitch estimation results.

### Parameters
- `times`: A NumPy array of shape `(n,)` representing the sample times of the pitch measurements.
- `frequencies`: A list of NumPy arrays containing the pitch measurements in Hz. Voicing is encoded by sign: positive values indicate voiced segments, while non-positive values indicate unvoiced segments.
- `midi`, default `False`: If `True`, the vertical axis is scaled to MIDI note numbers; otherwise, it uses a linear frequency scale (Hz).
- `unvoiced`, default `False`: If `True`, unvoiced pitches are rendered with transparency; if `False`, they are excluded from the visualization.
- `ax`, default `None`: A Matplotlib axis handle. If `None`, a new figure and axis are created.
- `prop_cycle`, default `None`: A `matplotlib.cycler.Cycler` object to define custom style properties for the plot.
- `**kwargs`: Additional keyword arguments passed directly to `matplotlib.pyplot.scatter`.

### Input
The function expects data in the format produced by `mir_eval.io.load_ragged_time_series`. Precondition: The input arrays must be aligned such that `times[i]` corresponds to the pitch measurements in `frequencies[i]`.

### Output
Returns `ax` (a `matplotlib.pyplot.axes._subplots.AxesSubplot` object), which is the handle to the axes containing the rendered pitch contours.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

times, pitches = mir_eval.io.load_ragged_time_series("path/to/est.txt")

# Standard visualization
mir_eval.display.multipitch(times, pitches, midi=False, unvoiced=True)
plt.show()
```

### LLM Instruction Prompt
- Always use `mir_eval.display.multipitch` as the entry point.
- Ensure `times` and `frequencies` are loaded via `mir_eval.io.load_ragged_time_series` before passing them to this function.
- When plotting, explicitly set `midi` and `unvoiced` boolean flags based on the desired visualization style.
- If customizing plot aesthetics, pass arguments via `**kwargs` that are compatible with `plt.scatter`.

### Prompt Snippet
```text
mir_eval.display.multipitch(times, frequencies, midi=True, unvoiced=False)
```

### Common Failure Modes
- **Mismatched Dimensions:** Passing `times` and `frequencies` that do not have corresponding lengths will result in a `ValueError` during plotting.
- **Invalid Data Format:** Providing raw data that has not been processed into the "ragged" format expected by `load_ragged_time_series` will cause indexing errors.
- **Missing Matplotlib:** The function requires an active Matplotlib environment; calling it in a headless environment without a backend configured (e.g., 'Agg') may cause display errors.

### Fix Code Hint
```python
# Ensure data is loaded correctly before visualization
times, pitches = mir_eval.io.load_ragged_time_series(file_path)
# If plotting fails due to format, verify the structure of the loaded arrays
assert len(times) == len(pitches)
mir_eval.display.multipitch(times, pitches)
```

## API Test: `mutual_information`

### Signature
```python
def mutual_information(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1)
```
_Source: source/mir_eval/segment.py:869_

_Source doc:_ Frame-clustering segmentation: mutual information metrics. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> mi, ami, nmi = mir_eval.structure.mutual_information(ref_intervals, ...                                                      ref_labels, ...                                                      est_intervals, ...                                                      est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) Returns ------- MI : float > 0 Mutual information between segmentations AMI : float Adjusted mutual information between segmentations. NMI : float > 0 Normalize mutual information between segmentations

### Goal
Computes information-theoretic metrics (MI, AMI, NMI) to evaluate the correspondence between a reference segmentation and an estimated segmentation by clustering them into fixed-size frames.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). The ground-truth segment start and end times.
- `reference_labels`: `list`, shape=(n,). The ground-truth labels corresponding to each interval.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). The estimated segment start and end times.
- `estimated_labels`: `list`, shape=(m,). The estimated labels corresponding to each interval.
- `frame_size`, default `0.1`: `float > 0`. The duration in seconds used to discretize the timeline for clustering.

### Input
The function expects data in the format returned by `mir_eval.io.load_labeled_intervals`. Before calling, users must ensure the estimated intervals are trimmed or padded to match the reference timing using `mir_eval.util.adjust_intervals` to ensure valid comparison.

### Output
Returns a tuple of three floats: `(MI, AMI, NMI)`, representing the Mutual Information, Adjusted Mutual Information, and Normalized Mutual Information between the two segmentations.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example from test suite
reference_intervals = np.array([[0, 1], [1, 2]])
estimated_intervals = np.array([[0, 1], [1, 2]])
labels = ["a", "b"]
mi, ami, nmi = mir_eval.segment.mutual_information(
    reference_intervals, labels, estimated_intervals, labels
)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are `np.ndarray` objects.
- Always use `mir_eval.util.adjust_intervals` to synchronize the time range of the estimated segmentation with the reference before passing them to this function.
- The function returns a tuple of three values; ensure the calling code unpacks all three.

### Prompt Snippet
```text
mi, ami, nmi = mir_eval.segment.mutual_information(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- **Mismatched Timings:** Failing to use `mir_eval.util.adjust_intervals` can lead to errors if the estimated segmentation duration does not align with the reference.
- **Invalid Frame Size:** Providing a non-positive `frame_size` will cause the metric calculation to fail.
- **Input Format:** Passing raw lists instead of `np.ndarray` for intervals may cause internal validation errors.

### Fix Code Hint
```python
# Ensure intervals are aligned before evaluation
est_intervals, est_labels = mir_eval.util.adjust_intervals(
    est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()
)
mi, ami, nmi = mir_eval.segment.mutual_information(ref_intervals, ref_labels, est_intervals, est_labels)
```

## API Test: `nce`

### Signature
```python
def nce(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1, beta=1.0, marginal=False)
```
_Source: source/mir_eval/segment.py:960_

_Source doc:_ Frame-clustering segmentation: normalized conditional entropy Computes cross-entropy of cluster assignment, normalized by the max-entropy. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> S_over, S_under, S_F = mir_eval.structure.nce(ref_intervals, ...                                               ref_labels, ...                                               est_intervals, ...                                               est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) beta : float > 0 beta for F-measure (Default value = 1.0) marginal : bool If `False`, normalize conditional entropy by uniform entropy. If `True`, normalize conditional entropy by the marginal entropy. (Default value = False) Returns ------- S_over Over-clustering score: - For `marginal=False`, ``1 - H(y_est | y_ref) / log(|y_est|)`` - For `marginal=True`, ``1 - H(y_est | y_ref) / H(y_est)`` If `|y_est|==1`, then `S_over` will be 0. S_under

### Goal
Computes the normalized conditional entropy (NCE) for music segmentation evaluation, measuring the cross-entropy of cluster assignments between reference and estimated segmentations.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2); ground-truth segment boundaries.
- `reference_labels`: `list`, shape=(n,); ground-truth segment labels.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2); predicted segment boundaries.
- `estimated_labels`: `list`, shape=(m,); predicted segment labels.
- `frame_size`, default `0.1`: `float > 0`; the duration in seconds for the frames used in clustering.
- `beta`, default `1.0`: `float > 0`; the beta parameter used for calculating the F-measure.
- `marginal`, default `False`: `bool`; determines the normalization method: `False` for uniform entropy, `True` for marginal entropy.

### Input
Requires segment intervals and labels as returned by `mir_eval.io.load_labeled_intervals`. Precondition: Intervals must be aligned and trimmed to match the same temporal range using `mir_eval.util.adjust_intervals` before evaluation to ensure valid comparison.

### Output
Returns a tuple `(S_over, S_under, S_F)` representing the over-clustering score, under-clustering score, and the F-measure based on the NCE calculation.

### Valid Call Patterns
```python
# Inferred from documentation examples
S_over, S_under, S_F = mir_eval.structure.nce(
    ref_intervals, 
    ref_labels, 
    est_intervals, 
    est_labels
)
```

### LLM Instruction Prompt
- Always use `mir_eval.structure.nce` to access this function.
- Ensure `reference_intervals` and `estimated_intervals` are pre-processed with `mir_eval.util.adjust_intervals` to ensure temporal alignment.
- The function returns three values: `S_over`, `S_under`, and `S_F`.

### Prompt Snippet
```text
# Example of NCE evaluation
S_over, S_under, S_F = mir_eval.structure.nce(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- Providing unaligned intervals (e.g., different start/end times) without using `mir_eval.util.adjust_intervals`.
- Passing `frame_size <= 0`, which violates the requirement for a positive float.
- Mismatched lengths between intervals and labels arrays.

### Fix Code Hint
```python
# Ensure intervals are aligned before calling nce
ref_intervals, ref_labels = mir_eval.util.adjust_intervals(ref_intervals, ref_labels, t_min=0)
est_intervals, est_labels = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
S_over, S_under, S_F = mir_eval.structure.nce(ref_intervals, ref_labels, est_intervals, est_labels)
```

## API Test: `occurrence_FPR`

### Signature
```python
def occurrence_FPR(reference_patterns, estimated_patterns, thres=0.75, similarity_metric='cardinality_score')
```
_Source: source/mir_eval/pattern.py:299_

_Source doc:_ Compute the occurrence F1 Score, Precision and Recall. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> F, P, R = mir_eval.pattern.occurrence_FPR(ref_patterns, ...                                           est_patterns) Parameters ---------- reference_patterns : list The reference patterns in the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format thres : float How similar two occurrences must be in order to be considered equal (Default value = .75) similarity_metric : str A string representing the metric to be used when computing the similarity matrix. Accepted values: - "cardinality_score": Count of the intersection between occurrences. (Default value = "cardinality_score") Returns ------- f_measure : float The occurrence F1 Score precision : float The occurrence Precision recall : float The occurrence Recall

### Goal
Computes the F-measure, precision, and recall for pattern occurrences by comparing estimated patterns against reference patterns based on a specified similarity threshold.

### Parameters
- `reference_patterns`: A list of reference patterns in the format returned by `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns in the same format as the reference patterns.
- `thres`, default `0.75`: A float defining the similarity threshold; two occurrences must meet or exceed this value to be considered equal.
- `similarity_metric`, default `'cardinality_score'`: A string specifying the metric used for the similarity matrix. Currently, only `"cardinality_score"` (the count of the intersection between occurrences) is supported.

### Input
The function expects two lists of patterns, typically loaded via `mir_eval.io.load_patterns()`. Precondition: Both `reference_patterns` and `estimated_patterns` must be formatted according to the library's internal pattern representation.

### Output
Returns a tuple of three floats: `(f_measure, precision, recall)`, representing the occurrence F1 Score, Precision, and Recall respectively.

### Valid Call Patterns
```python
import mir_eval
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
F, P, R = mir_eval.pattern.occurrence_FPR(ref_patterns, est_patterns)
```

### LLM Instruction Prompt
- When calling `occurrence_FPR`, ensure both `reference_patterns` and `estimated_patterns` are lists of patterns loaded via `mir_eval.io.load_patterns()`.
- The `similarity_metric` parameter is currently restricted to the string `'cardinality_score'`.
- The function returns a tuple of three floats; ensure your code unpacks these as `f_measure, precision, recall`.

### Prompt Snippet
```text
# Example of calling occurrence_FPR
f_measure, precision, recall = mir_eval.pattern.occurrence_FPR(ref_patterns, est_patterns, thres=0.8)
```

### Common Failure Modes
- **Format Mismatch:** Passing raw data that has not been processed by `mir_eval.io.load_patterns()` will likely result in an error or incorrect metric calculation.
- **Invalid Metric:** Providing a `similarity_metric` other than `'cardinality_score'` will cause the function to fail, as it is the only supported value.
- **Empty Inputs:** Passing empty lists for patterns may lead to division-by-zero errors or undefined behavior in the F-measure calculation.

### Fix Code Hint
```python
# Ensure patterns are loaded correctly before evaluation
import mir_eval
try:
    ref = mir_eval.io.load_patterns(ref_path)
    est = mir_eval.io.load_patterns(est_path)
    f, p, r = mir_eval.pattern.occurrence_FPR(ref, est)
except Exception as e:
    print(f"Evaluation failed: {e}")
```

## API Test: `offset_precision_recall_f1`

### Signature
```python
def offset_precision_recall_f1(ref_intervals, est_intervals, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False, beta=1.0)
```
_Source: source/mir_eval/transcription.py:705_

_Source doc:_ Compute the Precision, Recall and F-measure of note offsets: an estimated offset is considered correct if it is within +-50ms (or 20% of the ref note duration, which ever is greater) of a reference offset. Note that this metric completely ignores note onsets and note pitch. This means an estimated offset will be considered correct if it matches a reference offset, even if the offsets come from notes with completely different pitches (i.e. notes that would not match with :func:`match_notes`). Examples -------- >>> ref_intervals, _ = mir_eval.io.load_valued_intervals( ...     'reference.txt') >>> est_intervals, _ = mir_eval.io.load_valued_intervals( ...     'estimated.txt') >>> (offset_precision, ...  offset_recall, ...  offset_f_measure) = mir_eval.transcription.offset_precision_recall_f1( ...      ref_intervals, est_intervals) Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) offset_ratio : float > 0 or None The ratio of the reference note's duration used to define the offset_tolerance. Default is 0.2 (20%), meaning the ``offset_tolerance`` will equal the ``ref_duration * 0.2``, or ``offset_min_tolerance`` (0.05 by default, i.e. 50 ms), whichever is greater. offset_min_tolerance : float > 0 The minimum tolerance for offset matching. See ``offset_ratio`` description for an explanation of how the offset tolerance is determined. strict : bool If ``strict=False`` (the default), threshold checks for onset matching are performed using ``<=`` (less than or equal). If ``strict=True``, the threshold checks are performed using ``<`` (less than). beta : float > 0 Weighting factor for f-measure (default value = 1.0). Returns ------- precision : float The computed precision score recall : float The computed recall score f_measure : float The computed F-measure score

### Goal
Evaluates the temporal accuracy of note offsets in music transcription by calculating precision, recall, and F-measure based on a dynamic tolerance window relative to note duration.

### Parameters
- `ref_intervals`: A NumPy array of shape `(n, 2)` containing reference note intervals (onset and offset times).
- `est_intervals`: A NumPy array of shape `(m, 2)` containing estimated note intervals (onset and offset times).
- `offset_ratio`, default `0.2`: The ratio of the reference note duration used to calculate the tolerance window.
- `offset_min_tolerance`, default `0.05`: The minimum time (in seconds) allowed for an offset to be considered a match.
- `strict`, default `False`: If `False`, uses inclusive comparison (`<=`) for threshold checks; if `True`, uses exclusive comparison (`<`).
- `beta`, default `1.0`: The weighting factor for the F-measure calculation.

### Input
Requires two NumPy arrays representing time intervals. Data should be pre-processed into `(n, 2)` format, typically loaded via `mir_eval.io.load_valued_intervals` or similar utilities. Note that this metric ignores pitch and onset information.

### Output
Returns a tuple of three floats: `(precision, recall, f_measure)`.

### Valid Call Patterns
```python
import mir_eval
# Using standard interval arrays
precision, recall, f_measure = mir_eval.transcription.offset_precision_recall_f1(
    ref_int, est_int
)
```

### LLM Instruction Prompt
- Always pass `ref_intervals` and `est_intervals` as NumPy arrays of shape `(n, 2)`.
- Do not assume this function accounts for pitch or onset alignment; it is strictly for offset evaluation.
- Use `offset_ratio` and `offset_min_tolerance` to adjust the matching window for specific transcription task requirements.

### Prompt Snippet
```text
mir_eval.transcription.offset_precision_recall_f1(ref_intervals, est_intervals, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False, beta=1.0)
```

### Common Failure Modes
- Passing empty arrays: The function returns `(0, 0, 0)` if either input is empty.
- Incorrect array shape: Providing arrays that are not `(n, 2)` will cause internal NumPy indexing errors.
- Misinterpreting the metric: Users often expect this to evaluate note correctness (pitch/onset); remind them this only evaluates the temporal accuracy of the offset.

### Fix Code Hint
```python
# Ensure inputs are at least (0, 2) shaped arrays
import numpy as np
ref_int = np.array(ref_data) if len(ref_data) > 0 else np.empty((0, 2))
est_int = np.array(est_data) if len(est_data) > 0 else np.empty((0, 2))
```

## API Test: `onset_precision_recall_f1`

### Signature
```python
def onset_precision_recall_f1(ref_intervals, est_intervals, onset_tolerance=0.05, strict=False, beta=1.0)
```
_Source: source/mir_eval/transcription.py:643_

_Source doc:_ Compute the Precision, Recall and F-measure of note onsets: an estimated onset is considered correct if it is within +-50ms of a reference onset. Note that this metric completely ignores note offset and note pitch. This means an estimated onset will be considered correct if it matches a reference onset, even if the onsets come from notes with completely different pitches (i.e. notes that would not match with :func:`match_notes`). Examples -------- >>> ref_intervals, _ = mir_eval.io.load_valued_intervals( ...     'reference.txt') >>> est_intervals, _ = mir_eval.io.load_valued_intervals( ...     'estimated.txt') >>> (onset_precision, ...  onset_recall, ...  onset_f_measure) = mir_eval.transcription.onset_precision_recall_f1( ...      ref_intervals, est_intervals) Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) onset_tolerance : float > 0 The tolerance for an estimated note's onset deviating from the reference note's onset, in seconds. Default is 0.05 (50 ms). strict : bool If ``strict=False`` (the default), threshold checks for onset matching are performed using ``<=`` (less than or equal). If ``strict=True``, the threshold checks are performed using ``<`` (less than). beta : float > 0 Weighting factor for f-measure (default value = 1.0). Returns ------- precision : float The computed precision score recall : float The computed recall score f_measure : float The computed F-measure score

### Goal
Computes the precision, recall, and F-measure for note onsets by evaluating how many estimated onsets fall within a specified temporal tolerance of reference onsets, ignoring pitch and offset information.

### Parameters
- `ref_intervals`: A NumPy array of shape `(n, 2)` containing reference note intervals (onset and offset times).
- `est_intervals`: A NumPy array of shape `(m, 2)` containing estimated note intervals (onset and offset times).
- `onset_tolerance`, default `0.05`: A float representing the maximum allowed deviation (in seconds) between an estimated onset and a reference onset to be considered a match.
- `strict`, default `False`: A boolean flag; if `False`, matching uses a `less than or equal to` comparison; if `True`, it uses a `less than` comparison.
- `beta`, default `1.0`: A float representing the weighting factor for the F-measure calculation.

### Input
The function expects two NumPy arrays of shape `(n, 2)` and `(m, 2)`. These are typically loaded using `mir_eval.io.load_valued_intervals` or similar utilities that return interval data. Precondition: The input arrays must contain valid time intervals where the first column represents onset times.

### Output
Returns a tuple of three floats: `(precision, recall, f_measure)`.

### Valid Call Patterns
```python
import mir_eval
# Using standard interval arrays
precision, recall, f_measure = mir_eval.transcription.onset_precision_recall_f1(
    ref_int, est_int
)
```

### LLM Instruction Prompt
When calling `onset_precision_recall_f1`, ensure that inputs are NumPy arrays of shape `(n, 2)`. Do not attempt to pass pitch or offset data, as this function explicitly ignores them. If the input arrays are empty, the function will return `(0, 0, 0)`.

### Prompt Snippet
```text
import mir_eval.transcription
# Ensure ref_intervals and est_intervals are (N, 2) numpy arrays
precision, recall, f1 = mir_eval.transcription.onset_precision_recall_f1(ref_intervals, est_intervals)
```

### Common Failure Modes
- Passing arrays with incorrect dimensions (e.g., 1D arrays or arrays not shaped `(n, 2)`).
- Providing `onset_tolerance` values that are non-positive, which may lead to unexpected behavior or errors.
- Assuming the function considers pitch or duration; it is strictly an onset-timing metric.

### Fix Code Hint
```python
# Ensure inputs are properly shaped as (N, 2)
import numpy as np
ref_int = np.array(ref_data) # Ensure shape is (n, 2)
est_int = np.array(est_data) # Ensure shape is (m, 2)
precision, recall, f1 = mir_eval.transcription.onset_precision_recall_f1(ref_int, est_int)
```

## API Test: `overall_accuracy`

### Signature
```python
def overall_accuracy(ref_voicing, ref_cent, est_voicing, est_cent, cent_tolerance=50)
```
_Source: source/mir_eval/melody.py:691_

_Source doc:_ Compute the overall accuracy given two pitch (frequency) sequences in cents and matching voicing indicator sequences. The first pitch and voicing arrays are treated as the reference (truth), and the second two as the estimate (prediction).  All 4 sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> overall_accuracy = mir_eval.melody.overall_accuracy(ref_v, ref_c, ...                                                     est_v, est_c) Parameters ---------- ref_voicing : np.ndarray Reference voicing array. When this array is non-binary, it is treated as a 'reference reward', as in (Bittner & Bosch, 2019) ref_cent : np.ndarray Reference pitch sequence in cents est_voicing : np.ndarray Estimated voicing array est_cent : np.ndarray Estimate pitch sequence in cents cent_tolerance : float Maximum absolute deviation in cents for a frequency value to be considered correct (Default value = 50) Returns ------- overall_accuracy : float Overall accuracy, the total fraction of correctly estimates frames, where provides a correct frequency values (within cent_tolerance).

### Goal
Computes the overall accuracy of a melody transcription by calculating the fraction of frames where the estimated pitch and voicing state correctly match the reference within a specified cent tolerance.

### Parameters
- `ref_voicing`: `np.ndarray` representing the reference voicing state. If non-binary, it is treated as a 'reference reward' (Bittner & Bosch, 2019).
- `ref_cent`: `np.ndarray` containing the reference pitch sequence in cents.
- `est_voicing`: `np.ndarray` representing the estimated voicing state.
- `est_cent`: `np.ndarray` containing the estimated pitch sequence in cents.
- `cent_tolerance`, default `50`: `float` defining the maximum absolute deviation in cents allowed for a frequency estimate to be considered correct.

### Input
Requires four `np.ndarray` inputs of identical length. Data should be pre-processed into cent and voicing format, typically using `mir_eval.melody.to_cent_voicing` after loading raw time-series data via `mir_eval.io.load_time_series`.

### Output
Returns `float`: The overall accuracy, representing the total fraction of correctly estimated frames.

### Valid Call Patterns
```python
# Inferred from source documentation
accuracy = mir_eval.melody.overall_accuracy(ref_v, ref_c, est_v, est_c, cent_tolerance=50)
```

### LLM Instruction Prompt
- Ensure all four input arrays (`ref_voicing`, `ref_cent`, `est_voicing`, `est_cent`) are of the same length before calling.
- Use `mir_eval.melody.to_cent_voicing` to prepare inputs if starting from raw time-series data.
- The `cent_tolerance` parameter is a float; ensure it is set appropriately for the specific melody evaluation task requirements.

### Prompt Snippet
```text
# Example of preparing and calculating melody accuracy
ref_v, ref_c, est_v, est_c = mir_eval.melody.to_cent_voicing(ref_time, ref_freq, est_time, est_freq)
accuracy = mir_eval.melody.overall_accuracy(ref_v, ref_c, est_v, est_c, cent_tolerance=50)
```

### Common Failure Modes
- **Length Mismatch:** Passing arrays of different lengths will cause the function to fail, as it requires frame-by-frame alignment.
- **Unprepared Data:** Passing raw frequency values (Hz) instead of cents will result in incorrect accuracy scores.
- **Voicing Mismatch:** Providing voicing arrays that do not align with the pitch arrays (e.g., voicing indicates unvoiced but pitch array contains non-zero values) can lead to misleading results.

### Fix Code Hint
```python
# Ensure inputs are aligned and converted to cents
import numpy as np
# If lengths differ, resample or truncate to match
min_len = min(len(ref_voicing), len(est_voicing))
ref_v, ref_c = ref_voicing[:min_len], ref_cent[:min_len]
est_v, est_c = est_voicing[:min_len], est_cent[:min_len]
accuracy = mir_eval.melody.overall_accuracy(ref_v, ref_c, est_v, est_c)
```

## API Test: `overseg`

### Signature
```python
def overseg(reference_intervals, estimated_intervals)
```
_Source: source/mir_eval/chord.py:1409_

_Source doc:_ Compute the MIREX 'OverSeg' score. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> score = mir_eval.chord.overseg(ref_intervals, est_intervals) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2), dtype=float Reference chord intervals to score against. estimated_intervals : np.ndarray, shape=(m, 2), dtype=float Estimated chord intervals to score against. Returns ------- oversegmentation score : float Comparison score, in [0.0, 1.0], where 1.0 means no oversegmentation.

### Goal
Computes the MIREX 'OverSeg' score to quantify the degree of oversegmentation in estimated chord intervals relative to a ground-truth reference.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2), dtype=float. The ground-truth chord segment boundaries.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2), dtype=float. The estimated chord segment boundaries to be evaluated.

### Input
The function expects two NumPy arrays of shape (N, 2) and (M, 2) respectively, where each row represents a start and end time in seconds. These are typically obtained via `mir_eval.io.load_labeled_intervals`. Precondition: Intervals must be formatted as non-overlapping, chronologically ordered segments.

### Output
Returns a `float` representing the oversegmentation score in the range [0.0, 1.0], where 1.0 indicates no oversegmentation (i.e., the estimated intervals do not unnecessarily subdivide the reference intervals).

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example using test-suite style arrays
ref_ivs = np.array([[0.0, 2.0], [2.0, 2.5], [2.5, 3.2]])
est_ivs = np.array([[0.0, 3.0], [3.0, 3.5]])
score = mir_eval.chord.overseg(ref_ivs, est_ivs)
```

### LLM Instruction Prompt
- Always use the `mir_eval.chord.overseg` namespace.
- Ensure inputs are `np.ndarray` objects with shape (N, 2) containing float values.
- The function is intended for chord segmentation evaluation; ensure the intervals represent time-based segments.

### Prompt Snippet
```text
score = mir_eval.chord.overseg(reference_intervals, estimated_intervals)
```

### Common Failure Modes
- Passing arrays with incorrect shapes (e.g., missing the second dimension for start/end times).
- Providing intervals that are not sorted or contain overlaps, which may lead to unexpected score calculations.
- Passing non-NumPy types (e.g., lists) which may cause internal `mir_eval` validation errors.

### Fix Code Hint
```python
# Ensure inputs are properly formatted as NumPy arrays
import numpy as np
ref_ivs = np.array(ref_list, dtype=float)
est_ivs = np.array(est_list, dtype=float)
score = mir_eval.chord.overseg(ref_ivs, est_ivs)
```

## API Test: `p_score`

### Signature
```python
def p_score(reference_beats, estimated_beats, p_score_threshold=0.2)
```
_Source: source/mir_eval/beat.py:329_

_Source doc:_ Get McKinney's P-score. Based on the autocorrelation of the reference and estimated beats Examples -------- >>> reference_beats = mir_eval.io.load_events('reference.txt') >>> reference_beats = mir_eval.beat.trim_beats(reference_beats) >>> estimated_beats = mir_eval.io.load_events('estimated.txt') >>> estimated_beats = mir_eval.beat.trim_beats(estimated_beats) >>> p_score = mir_eval.beat.p_score(reference_beats, estimated_beats) Parameters ---------- reference_beats : np.ndarray reference beat times, in seconds estimated_beats : np.ndarray query beat times, in seconds p_score_threshold : float Window size will be ``p_score_threshold*np.median(inter_annotation_intervals)``, (Default value = 0.2) Returns ------- correlation : float McKinney's P-score

### Goal
Computes McKinney's P-score, a metric that evaluates beat tracking accuracy by comparing the autocorrelation of reference and estimated beat sequences.

### Parameters
- `reference_beats`: `np.ndarray` containing ground-truth beat times in seconds.
- `estimated_beats`: `np.ndarray` containing the estimated beat times in seconds.
- `p_score_threshold`, default `0.2`: `float` used to define the window size for the autocorrelation comparison, calculated as `p_score_threshold * np.median(inter_annotation_intervals)`.

### Input
- **Data:** Expects 1D `np.ndarray` objects representing time-stamped events.
- **Preconditions:** Inputs should be preprocessed (e.g., using `mir_eval.beat.trim_beats`) to ensure they are cleaned and aligned before evaluation. Annotations should be loaded using `mir_eval.io.load_events`.

### Output
Returns `correlation` (`float`): The calculated McKinney's P-score, representing the similarity between the autocorrelation of the reference and estimated beat sequences.

### Valid Call Patterns
```python
import mir_eval
# Inferred from source documentation
p_score = mir_eval.beat.p_score(reference_beats, estimated_beats)
p_score = mir_eval.beat.p_score(reference_beats, estimated_beats, p_score_threshold=0.2)
```

### LLM Instruction Prompt
- When calling `p_score`, ensure both `reference_beats` and `estimated_beats` are provided as `np.ndarray` objects.
- Always perform `mir_eval.beat.trim_beats` on input arrays prior to calling `p_score` to ensure consistent evaluation.
- The `p_score_threshold` parameter is optional and defaults to 0.2; only modify it if specific windowing requirements are needed for the evaluation context.

### Prompt Snippet
```text
# Example of calculating P-score
reference = mir_eval.io.load_events('ref.txt')
estimated = mir_eval.io.load_events('est.txt')
score = mir_eval.beat.p_score(mir_eval.beat.trim_beats(reference), mir_eval.beat.trim_beats(estimated))
```

### Common Failure Modes
- **Input Type Mismatch:** Passing lists instead of `np.ndarray` objects may cause internal calculation errors.
- **Unprocessed Data:** Failing to trim beats can lead to inaccurate scores if the input files contain leading or trailing silence/non-beat events.
- **Empty Arrays:** Passing empty arrays will result in errors during the calculation of the median inter-annotation interval.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays and trimmed
ref = np.array(reference_beats)
est = np.array(estimated_beats)
score = mir_eval.beat.p_score(mir_eval.beat.trim_beats(ref), mir_eval.beat.trim_beats(est))
```

## API Test: `pairwise`

### Signature
```python
def pairwise(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1, beta=1.0)
```
_Source: source/mir_eval/segment.py:310_

_Source doc:_ Frame-clustering segmentation evaluation by pair-wise agreement. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> precision, recall, f = mir_eval.structure.pairwise(ref_intervals, ...                                                    ref_labels, ...                                                    est_intervals, ...                                                    est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m, ) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) beta : float > 0 beta value for F-measure (Default value = 1.0) Returns ------- precision : float > 0 Precision of detecting whether frames belong in the same cluster recall : float > 0 Recall of detecting whether frames belong in the same cluster f : float > 0 F-measure of detecting whether frames belong in the same cluster

### Goal
Evaluates music segmentation by measuring the pairwise agreement between reference and estimated segment labels across discretized time frames.

### Parameters
- `reference_intervals`: `np.ndarray` of shape `(n, 2)` containing the start and end times of reference segments.
- `reference_labels`: `list` of length `n` containing the labels corresponding to each reference interval.
- `estimated_intervals`: `np.ndarray` of shape `(m, 2)` containing the start and end times of estimated segments.
- `estimated_labels`: `list` of length `m` containing the labels corresponding to each estimated segment.
- `frame_size`, default `0.1`: The duration in seconds used to discretize the timeline for clustering evaluation.
- `beta`, default `1.0`: The weighting factor for the F-measure calculation.

### Input
Data must be provided as NumPy arrays for intervals and lists for labels, typically obtained via `mir_eval.io.load_labeled_intervals`. Precondition: The estimated intervals must be trimmed or padded to match the reference timing using `mir_eval.util.adjust_intervals` before evaluation to ensure temporal alignment.

### Output
Returns a tuple of three floats: `(precision, recall, f)`, representing the precision, recall, and F-measure of the pairwise frame-clustering agreement.

### Valid Call Patterns
```python
# Inferred from source documentation
precision, recall, f = mir_eval.structure.pairwise(
    ref_intervals, 
    ref_labels, 
    est_intervals, 
    est_labels
)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are aligned using `mir_eval.util.adjust_intervals` before passing them to `pairwise`.
- Ensure `frame_size` and `beta` are positive floats.
- The function is located in `mir_eval.structure`.

### Prompt Snippet
```text
import mir_eval
# Ensure intervals are adjusted to match before calling
precision, recall, f = mir_eval.structure.pairwise(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- Mismatched temporal bounds between reference and estimated intervals (must use `adjust_intervals` to resolve).
- Passing non-NumPy arrays for intervals or non-list types for labels.
- Providing `frame_size` or `beta` values less than or equal to zero, which will cause invalid metric computation.

### Fix Code Hint
```python
# Use adjust_intervals to ensure temporal consistency
ref_intervals, ref_labels = mir_eval.util.adjust_intervals(ref_intervals, ref_labels, t_min=0)
est_intervals, est_labels = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
```

## API Test: `percentage_correct`

### Signature
```python
def percentage_correct(reference_timestamps, estimated_timestamps, window=0.3)
```
_Source: source/mir_eval/alignment.py:144_

_Source doc:_ Compute the percentage of correctly predicted timestamps. A timestamp is predicted correctly if its position doesn't deviate more than the window parameter from the ground truth timestamp. Examples -------- >>> reference_timestamps = mir_eval.io.load_events('reference.txt') >>> estimated_timestamps = mir_eval.io.load_events('estimated.txt') >>> pc = mir_eval.align.percentage_correct(reference_onsets, estimated_timestamps, window=0.2) Parameters ---------- reference_timestamps : np.ndarray reference timestamps, in seconds estimated_timestamps : np.ndarray estimated timestamps, in seconds window : float Window size, in seconds (Default value = .3) Returns ------- pc : float Percentage of correct timestamps

### Goal
Computes the accuracy of timestamp predictions by calculating the proportion of estimated events that fall within a specified temporal tolerance window of the ground truth.

### Parameters
- `reference_timestamps`: A NumPy array of ground truth timestamps in seconds.
- `estimated_timestamps`: A NumPy array of estimated timestamps in seconds.
- `window`, default `0.3`: The temporal tolerance threshold in seconds; an estimate is considered correct if its absolute deviation from a reference timestamp is less than or equal to this value.

### Input
- **Data Types:** Expects `np.ndarray` objects containing numerical timestamps.
- **Preconditions:** Timestamps must be provided in seconds. It is recommended to use `mir_eval.io.load_events` to ensure input data is correctly parsed from standard text-based annotation files before passing to this function.

### Output
Returns `pc` (float): The percentage of estimated timestamps that are correctly predicted, represented as a value between 0.0 and 1.0.

### Valid Call Patterns
```python
import mir_eval
# Inferred from documentation examples
reference = mir_eval.io.load_events('reference.txt')
estimated = mir_eval.io.load_events('estimated.txt')
pc = mir_eval.align.percentage_correct(reference, estimated, window=0.2)
```

### LLM Instruction Prompt
- Always use `mir_eval.align.percentage_correct` as the function path.
- Ensure both `reference_timestamps` and `estimated_timestamps` are provided as `np.ndarray` objects.
- If the window size is not specified by the user, default to `0.3`.

### Prompt Snippet
```text
mir_eval.align.percentage_correct(reference_timestamps, estimated_timestamps, window=0.3)
```

### Common Failure Modes
- **Type Mismatch:** Passing lists instead of `np.ndarray` objects may cause internal validation errors.
- **Unit Mismatch:** Providing timestamps in samples rather than seconds will result in incorrect evaluation.
- **Empty Arrays:** Passing empty arrays for either reference or estimated timestamps may lead to division by zero or unexpected return values depending on the implementation.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays
ref = np.array(reference_list)
est = np.array(estimated_list)

# Compute percentage correct with a custom window
score = mir_eval.align.percentage_correct(ref, est, window=0.5)
```

## API Test: `percentage_correct_segments`

### Signature
```python
def percentage_correct_segments(reference_timestamps, estimated_timestamps, duration: Optional[float]=None)
```
_Source: source/mir_eval/alignment.py:175_

_Source doc:_ Calculate the percentage of correct segments (PCS) metric. It constructs segments out of predicted and estimated timestamps separately out of each given timestamp vector and calculates the percentage of overlap between correct segments compared to the total duration. WARNING: This metrics behaves differently depending on whether "duration" is given! If duration is not given (default case), the computation follows the MIREX lyrics alignment challenge 2020. For a timestamp vector with entries (t1,t2, ... tN), segments with the following (start, end) boundaries are created: (t1, t2), ... (tN-1, tN). After the segments are created, the overlap between the reference and estimated segments is determined and divided by the total duration, which is the distance between the first and last timestamp in the reference. If duration is given, the segment boundaries are instead (0, t1), (t1, t2), ... (tN, duration). The overlap is computed in the same way, but then divided by the duration parameter given to this function. This method follows the original paper [#fujihara2011] more closely, where the metric was proposed. As a result, this variant of the metrics punishes cases where the first estimated timestamp is too early or the last estimated timestamp is too late, whereas the MIREX variant does not. On the other hand, the MIREX metric is invariant to how long the eventless beginning and end parts of the audio are, which might be a desirable property. Examples -------- >>> reference_timestamps = mir_eval.io.load_events('reference.txt') >>> estimated_timestamps = mir_eval.io.load_events('estimated.txt') >>> pcs = mir_eval.align.percentage_correct_segments(reference_timestamps, estimated_timestamps) Parameters ---------- reference_timestamps : np.ndarray reference timestamps, in seconds estimated_timestamps : np.ndarray estimated timestamps, in seconds duration : float Optional. Total duration of audio (seconds). WARNING: Metric is computed differently depending on whether this is provided or not - see documentation above! Returns ------- pcs : float Percentage of time where ground truth and predicted segments overlap

### Goal
Calculates the Percentage of Correct Segments (PCS) by measuring the temporal overlap between reference and estimated segment boundaries derived from timestamp vectors.

### Parameters
- `reference_timestamps`: A `np.ndarray` of ground truth timestamps in seconds.
- `estimated_timestamps`: A `np.ndarray` of estimated timestamps in seconds.
- `duration` (`Optional[float]`), default `None`: The total duration of the audio in seconds. If provided, segment boundaries include the intervals from 0 to the first timestamp and the last timestamp to the duration.

### Input
- **Data:** Expects `np.ndarray` objects containing timestamps in seconds.
- **Preconditions:** Timestamps should be loaded using `mir_eval.io.load_events` or equivalent, ensuring they are sorted chronologically.
- **Format:** The input vectors represent event points; the function internally constructs segments based on these points.

### Output
Returns `pcs` (`float`): The percentage of time where ground truth and predicted segments overlap, normalized by the total duration (either the span of the reference timestamps or the provided `duration` parameter).

### Valid Call Patterns
```python
import mir_eval
# Using default MIREX-style calculation
pcs = mir_eval.align.percentage_correct_segments(reference_timestamps, estimated_timestamps)

# Using explicit duration for Fujihara et al. (2011) style calculation
pcs = mir_eval.align.percentage_correct_segments(reference_timestamps, estimated_timestamps, duration=30.0)
```

### LLM Instruction Prompt
- When calling `percentage_correct_segments`, always clarify if the `duration` parameter is intended to be used, as it fundamentally changes the metric's behavior regarding eventless audio segments.
- Ensure inputs are `np.ndarray` objects.

### Prompt Snippet
```text
mir_eval.align.percentage_correct_segments(ref_ts, est_ts, duration=None)
```

### Common Failure Modes
- **Mismatched Units:** Providing timestamps in samples instead of seconds will result in incorrect overlap calculations.
- **Unsorted Timestamps:** If the input arrays are not sorted, the segment construction logic will produce invalid intervals.
- **Duration Mismatch:** Providing a `duration` shorter than the last timestamp in the input vectors may lead to unexpected behavior in segment boundary creation.

### Fix Code Hint
```python
# Ensure timestamps are sorted before evaluation
reference_timestamps = np.sort(reference_timestamps)
estimated_timestamps = np.sort(estimated_timestamps)
pcs = mir_eval.align.percentage_correct_segments(reference_timestamps, estimated_timestamps, duration=total_audio_duration)
```

## API Test: `piano_roll`

### Signature
```python
def piano_roll(intervals, pitches=None, midi=None, ax=None, **kwargs)
```
_Source: source/mir_eval/display.py:873_

_Source doc:_ Plot a quantized piano roll as intervals Parameters ---------- intervals : np.ndarray, shape=(n, 2) timing intervals for notes pitches : np.ndarray, shape=(n,), optional pitches of notes (in Hz). midi : np.ndarray, shape=(n,), optional pitches of notes (in MIDI numbers). At least one of ``pitches`` or ``midi`` must be provided. ax : matplotlib.pyplot.axes An axis handle on which to draw the intervals. If none is provided, a new set of axes is created. **kwargs Additional keyword arguments to :func:`labeled_intervals`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes note events as a quantized piano roll plot, mapping time intervals to the x-axis and pitch values to the y-axis.

### Parameters
- `intervals`: A NumPy array of shape `(n, 2)` representing the start and end times of `n` notes.
- `pitches`, default `None`: A NumPy array of shape `(n,)` representing the pitch of each note in Hz.
- `midi`, default `None`: A NumPy array of shape `(n,)` representing the pitch of each note in MIDI note numbers.
- `ax`, default `None`: A `matplotlib.pyplot.axes` object. If `None`, a new figure and axes are created.
- `**kwargs`: Additional keyword arguments passed to `labeled_intervals` (e.g., `label`, `alpha`, `facecolor`).

### Input
- **Data:** Requires `intervals` as a `(n, 2)` NumPy array.
- **Preconditions:** At least one of `pitches` or `midi` must be provided. If using `pitches` in Hz, ensure they are in a format compatible with the plot scale. If using `midi` numbers, ensure they are correctly mapped from frequency data using `mir_eval.util.hz_to_midi` if necessary.

### Output
Returns `matplotlib.pyplot.axes._subplots.AxesSubplot`: A handle to the axes containing the rendered piano roll.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

# Using pitches in Hz
mir_eval.display.piano_roll(ref_t, ref_p, label="Reference", alpha=0.5)

# Using MIDI numbers
mir_eval.display.piano_roll(ref_t, midi=ref_midi, label="Reference", alpha=0.5)
```

### LLM Instruction Prompt
- When calling `piano_roll`, you must provide `intervals` and at least one of `pitches` or `midi`.
- Always ensure the input arrays are NumPy arrays of consistent length `n`.
- If you need to plot multiple datasets on the same figure, pass the same `ax` handle to subsequent calls or use `plt.figure()` before calling the function.

### Prompt Snippet
```text
mir_eval.display.piano_roll(intervals, pitches=pitches, midi=midi, ax=ax, **kwargs)
```

### Common Failure Modes
- **Missing Pitch Data:** Providing neither `pitches` nor `midi` will result in an error as the function cannot determine the vertical position of the notes.
- **Shape Mismatch:** Providing `intervals` and `pitches`/`midi` arrays that do not have the same number of rows `n` will cause a broadcast or indexing error.
- **Matplotlib Context:** Calling the function without an active figure or axes when `ax=None` may lead to unexpected behavior in non-interactive environments.

### Fix Code Hint
```python
# Ensure pitch data is converted to MIDI if necessary for consistent scaling
midi_data = mir_eval.util.hz_to_midi(pitches)
mir_eval.display.piano_roll(intervals, midi=midi_data)
```

## API Test: `pitch`

### Signature
```python
def pitch(times, frequencies, midi=False, unvoiced=False, ax=None, prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:659_

_Source doc:_ Visualize pitch contours Parameters ---------- times : np.ndarray, shape=(n,) Sample times of frequencies frequencies : np.ndarray, shape=(n,) frequencies (in Hz) of the pitch contours. Voicing is indicated by sign (positive for voiced, non-positive for non-voiced). midi : bool If `True`, plot on a MIDI-numbered vertical axis. Otherwise, plot on a linear frequency axis. unvoiced : bool If `True`, unvoiced pitch contours are plotted and indicated by transparency. Otherwise, unvoiced pitch contours are omitted from the display. ax : matplotlib.pyplot.axes An axis handle on which to draw the pitch contours. If none is provided, a new set of axes is created. prop_cycle : cycle.Cycler An optional property cycle object to specify style properties. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to `matplotlib.pyplot.plot`. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes pitch contours by plotting frequency over time, supporting both linear frequency and MIDI-note vertical axes.

### Parameters
- `times`: `np.ndarray`, shape=(n,). The sample times corresponding to the frequency values.
- `frequencies`: `np.ndarray`, shape=(n,). The pitch contour frequencies in Hz. Voicing is indicated by the sign: positive values represent voiced segments, while non-positive values represent unvoiced segments.
- `midi`, default `False`: If `True`, the vertical axis is scaled to MIDI note numbers; otherwise, it uses a linear frequency scale.
- `unvoiced`, default `False`: If `True`, unvoiced segments are rendered with transparency; if `False`, they are omitted from the plot.
- `ax`, default `None`: A `matplotlib.pyplot.axes` handle. If `None`, a new figure and axes are created.
- `prop_cycle`, default `None`: An optional `cycler.Cycler` object to define plot style properties.
- `**kwargs`: Additional keyword arguments passed directly to `matplotlib.pyplot.plot`.

### Input
The function expects two `np.ndarray` objects of equal length `n`. Data should be pre-processed into time-frequency pairs, typically loaded via `mir_eval.io` or generated by melody/pitch extraction algorithms.

### Output
Returns `matplotlib.pyplot.axes._subplots.AxesSubplot`: A handle to the axes object containing the rendered pitch contour plot.

### Valid Call Patterns
```python
# Plotting on a Hz scale
mir_eval.display.pitch(ref_times, ref_freqs, unvoiced=True, label="Reference")

# Plotting on a MIDI scale
mir_eval.display.pitch(times, freqs, midi=True)
```

### LLM Instruction Prompt
- When calling `mir_eval.display.pitch`, ensure `times` and `frequencies` are NumPy arrays of the same length.
- Always specify `midi=True` if the intended visualization is for musical note analysis.
- Use `unvoiced=True` if the input data contains non-positive frequency values that should be visualized rather than ignored.
- Pass additional styling arguments (e.g., `color`, `linestyle`) via `**kwargs`.

### Prompt Snippet
```text
import mir_eval.display
import matplotlib.pyplot as plt

# Assuming times and freqs are defined
ax = mir_eval.display.pitch(times, freqs, midi=True, unvoiced=True)
plt.show()
```

### Common Failure Modes
- **Shape Mismatch:** Passing `times` and `frequencies` arrays of different lengths will raise a `ValueError` during plotting.
- **Unvoiced Data Omission:** If `unvoiced=False` (default), all non-positive frequency values will be excluded from the plot, which may result in empty or fragmented contours if the input is entirely unvoiced.
- **Axis Confusion:** Forgetting to set `midi=True` when plotting musical pitch data will result in a linear frequency plot, which may make musical intervals difficult to interpret.

### Fix Code Hint
```python
# Ensure arrays are aligned and handle unvoiced data explicitly
import numpy as np
times = np.array(times)
freqs = np.array(freqs)
if times.shape == freqs.shape:
    mir_eval.display.pitch(times, freqs, midi=True, unvoiced=True)
```

## API Test: `pitch_class_to_semitone`

### Signature
```python
def pitch_class_to_semitone(pitch_class)
```
_Source: source/mir_eval/chord.py:142_

_Source doc:_ Convert a pitch class to semitone. Parameters ---------- pitch_class : str Spelling of a given pitch class, e.g. 'C#', 'Gbb' Returns ------- semitone : int Semitone value of the pitch class.

### Goal
Converts a string representation of a musical pitch class (e.g., 'C#', 'Gbb') into its corresponding integer semitone value (0-11) for use in chord recognition and harmonic analysis tasks.

### Parameters
- `pitch_class`: `str`. The spelling of a given pitch class.

### Input
The input must be a string representing a valid musical pitch class. The function expects standard scientific or musical notation for pitch classes, including accidentals (e.g., 'C', 'C#', 'Db', 'Gbb').

### Output
Returns `int`. The semitone value of the pitch class, typically mapped as C=0, C#/Db=1, ..., B=11.

### Valid Call Patterns
```python
import mir_eval
# Example of converting a pitch class to its semitone integer
semitone = mir_eval.chord.pitch_class_to_semitone('C#')
```

### LLM Instruction Prompt
When using `pitch_class_to_semitone`, ensure the input is a string representing a valid pitch class. The function is located within the `mir_eval.chord` submodule. Do not attempt to pass numerical values or lists; the function expects a single string argument.

### Prompt Snippet
```text
mir_eval.chord.pitch_class_to_semitone(pitch_class)
```

### Common Failure Modes
- **Invalid String Format:** Passing a string that does not correspond to a recognized musical pitch class (e.g., 'H' or 'XYZ') will result in an error.
- **Incorrect Type:** Passing an integer or a list instead of a string will cause a `TypeError` or `AttributeError` during string processing.
- **Case Sensitivity:** Depending on the internal implementation, passing lowercase letters (e.g., 'c#') may fail if the function strictly expects uppercase notation.

### Fix Code Hint
```python
# Ensure the input is a string and properly formatted
try:
    semitone = mir_eval.chord.pitch_class_to_semitone(pitch_class.upper())
except Exception as e:
    print(f"Invalid pitch class provided: {e}")
```

## API Test: `pitch_contour`

### Signature
```python
def pitch_contour(times, frequencies, fs, amplitudes=None, function=np.sin, length=None, kind='linear')
```
_Source: source/mir_eval/sonify.py:247_

_Source doc:_ Sonify a pitch contour. Parameters ---------- times : np.ndarray time indices for each frequency measurement, in seconds frequencies : np.ndarray frequency measurements, in Hz. Non-positive measurements or NaNs will be interpreted as un-voiced samples. fs : int desired sampling rate of the output signal amplitudes : np.ndarray amplitude measurements, nonnegative defaults to ``np.ones((length,))`` function : function function to use to synthesize notes, should be 2π-periodic length : int desired number of samples in the output signal, defaults to ``max(times)*fs`` kind : str Interpolation mode for the frequency and amplitude values. See: ``scipy.interpolate.interp1d`` for valid settings. Returns ------- output : np.ndarray synthesized version of the pitch contour

### Goal
Synthesize an audio signal from a time-frequency pitch contour for auditory verification of MIR transcription results.

### Parameters
- `times`: `np.ndarray` of time indices for each frequency measurement, in seconds.
- `frequencies`: `np.ndarray` of frequency measurements, in Hz. Non-positive values or NaNs are treated as un-voiced.
- `fs`: `int` representing the desired sampling rate of the output signal.
- `amplitudes`, default `None`: `np.ndarray` of non-negative amplitude measurements. Defaults to a constant array of ones if `None`.
- `function`, default `np.sin`: A 2π-periodic function used to synthesize the waveform.
- `length`, default `None`: `int` specifying the total number of samples in the output signal. Defaults to `max(times) * fs`.
- `kind`, default `'linear'`: `str` defining the interpolation mode for frequency and amplitude values (passed to `scipy.interpolate.interp1d`).

### Input
Requires `numpy` arrays for `times`, `frequencies`, and optionally `amplitudes`. The `times` and `frequencies` arrays must be of equal length. Precondition: `frequencies` should contain values in Hz; non-positive values or `NaN`s are interpreted as silence (un-voiced).

### Output
Returns `np.ndarray` representing the synthesized audio signal at the specified sampling rate `fs`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Basic usage
x = mir_eval.sonify.pitch_contour(times, freqs, fs)

# Usage with explicit length and amplitude
x = mir_eval.sonify.pitch_contour(times, freqs, fs, length=fs * 7, amplitudes=amps)
```

### LLM Instruction Prompt
- Always use the `mir_eval.sonify.pitch_contour` namespace.
- Ensure `times` and `frequencies` are provided as `np.ndarray` objects.
- If `length` is provided, ensure it is an integer representing the total sample count.
- Remember that non-positive values in `frequencies` are automatically handled as un-voiced (silence).

### Prompt Snippet
```text
mir_eval.sonify.pitch_contour(times, frequencies, fs, amplitudes=None, length=None)
```

### Common Failure Modes
- **Dimension Mismatch:** Providing `times` and `frequencies` arrays of different lengths will raise a `ValueError` during interpolation.
- **Negative Amplitudes:** Providing negative values in the `amplitudes` array may lead to unexpected synthesis behavior or errors.
- **Interpolation Errors:** Using an invalid `kind` string that is not supported by `scipy.interpolate.interp1d` will raise an error.
- **Boundary Conditions:** If `length` is set such that the sampling grid extends beyond the provided `times` range, the function will produce constant values at the boundaries.

### Fix Code Hint
```python
# Ensure arrays are numpy arrays and lengths match
times = np.array(times)
frequencies = np.array(frequencies)
if len(times) != len(frequencies):
    raise ValueError("times and frequencies must have the same length")
```

## API Test: `precision_recall_f1_overlap`

### Signature
```python
def precision_recall_f1_overlap(ref_intervals, ref_pitches, ref_velocities, est_intervals, est_pitches, est_velocities, onset_tolerance=0.05, pitch_tolerance=50.0, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False, velocity_tolerance=0.1, beta=1.0)
def precision_recall_f1_overlap(ref_intervals, ref_pitches, est_intervals, est_pitches, onset_tolerance=0.05, pitch_tolerance=50.0, offset_ratio=0.2, offset_min_tolerance=0.05, strict=False, beta=1.0)
```
_Source: source/mir_eval/transcription_velocity.py:230  (+1 more definition site/overload)_

_Source doc:_ Compute the Precision, Recall and F-measure of correct vs incorrectly transcribed notes, and the Average Overlap Ratio for correctly transcribed notes (see :func:`mir_eval.transcription.average_overlap_ratio`). "Correctness" is determined based on note onset, velocity, pitch and (optionally) offset. An estimated note is considered correct if 1. Its onset is within ``onset_tolerance`` (default +-50ms) of a reference note 2. Its pitch (F0) is within +/- ``pitch_tolerance`` (default one quarter tone, 50 cents) of the corresponding reference note 3. Its velocity, after normalizing reference velocities to the range [0, 1] and globally rescaling estimated velocities to minimize L2 distance between matched reference notes, is within ``velocity_tolerance`` (default 0.1) the corresponding reference note 4. If ``offset_ratio`` is ``None``, note offsets are ignored in the comparison. Otherwise, on top of the above requirements, a correct returned note is required to have an offset value within `offset_ratio`` (default 20%) of the reference note's duration around the reference note's offset, or within ``offset_min_tolerance`` (default 50 ms), whichever is larger.

### Goal
Computes note-level transcription accuracy metrics (Precision, Recall, F-measure) and the Average Overlap Ratio by matching estimated notes against reference notes based on onset, pitch, velocity, and optional offset criteria.

### Parameters
- `ref_intervals`: `np.ndarray`, shape=(n,2). Array of reference note onset and offset times in seconds.
- `ref_pitches`: `np.ndarray`, shape=(n,). Array of reference pitch values in Hertz.
- `ref_velocities`: `np.ndarray`, shape=(n,). Array of MIDI velocities (0-127) for reference notes.
- `est_intervals`: `np.ndarray`, shape=(m,2). Array of estimated note onset and offset times in seconds.
- `est_pitches`: `np.ndarray`, shape=(m,). Array of estimated pitch values in Hertz.
- `est_velocities`: `np.ndarray`, shape=(n,). Array of MIDI velocities (0-127) for estimated notes.
- `onset_tolerance`, default `0.05`: Tolerance for onset deviation in seconds.
- `pitch_tolerance`, default `50.0`: Tolerance for pitch deviation in cents.
- `offset_ratio`, default `0.2`: Ratio of reference duration to define offset tolerance; if `None`, offsets are ignored.
- `offset_min_tolerance`, default `0.05`: Minimum threshold for offset matching in seconds.
- `strict`, default `False`: If `False`, uses `<=` for threshold checks; if `True`, uses `<`.
- `velocity_tolerance`, default `0.1`: Tolerance for velocity matching after global rescaling.
- `beta`, default `1.0`: Weight of recall in the F-measure calculation.

### Input
Requires NumPy arrays for all inputs. Reference and estimated intervals must be formatted as `(n, 2)` arrays. Pitches and velocities must be 1D arrays of length `n` and `m` respectively. Precondition: Ensure all time values are in seconds and pitches are in Hertz.

### Output
Returns a tuple of four floats: `(precision, recall, f_measure, avg_overlap_ratio)`.

### Valid Call Patterns
```python
import mir_eval
# Standard call with velocity
precision, recall, f1, overlap = mir_eval.transcription.precision_recall_f1_overlap(
    ref_int, ref_pitch, ref_vel, est_int, est_pitch, est_vel
)

# Call without velocity (using overload)
precision, recall, f1, overlap = mir_eval.transcription.precision_recall_f1_overlap(
    ref_int, ref_pitch, est_int, est_pitch, offset_ratio=None
)
```

### LLM Instruction Prompt
- Always use the `mir_eval.transcription` namespace.
- If velocity data is unavailable, use the overload that excludes `ref_velocities` and `est_velocities`.
- Ensure all interval arrays are `np.ndarray` with shape `(N, 2)`.
- When setting `offset_ratio=None`, ensure the logic accounts for the fact that offset matching will be disabled.

### Prompt Snippet
```text
# Example of calling the function
precision, recall, f1, overlap = mir_eval.transcription.precision_recall_f1_overlap(
    ref_intervals, ref_pitches, ref_velocities, est_intervals, est_pitches, est_velocities
)
```

### Common Failure Modes
- Passing lists instead of `np.ndarray` objects, which may cause indexing errors.
- Mismatched array lengths between intervals, pitches, and velocities for the same set (e.g., `ref_intervals` length != `ref_pitches` length).
- Providing velocity values outside the expected 0-127 MIDI range, which may lead to unexpected rescaling behavior.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays
ref_int = np.array(ref_int)
ref_pitch = np.array(ref_pitch)
# Check lengths
assert len(ref_int) == len(ref_pitch)
```

## API Test: `quality_to_bitmap`

### Signature
```python
def quality_to_bitmap(quality)
```
_Source: source/mir_eval/chord.py:276_

_Source doc:_ Return the bitmap for a given quality. Parameters ---------- quality : str Chord quality name. Returns ------- bitmap : np.ndarray Bitmap representation of this quality (12-dim).

### Goal
Converts a string-based chord quality name into a 12-dimensional binary NumPy array representing the pitch class content of that quality.

### Parameters
- `quality`: `str`. The name of the chord quality (e.g., "maj", "min").

### Input
- **Data**: A string representing a chord quality.
- **Preconditions**: The provided string must exist within the library's internal `QUALITIES` list. Input validation is performed internally; providing an unrecognized quality string will trigger an exception.

### Output
Returns `np.ndarray` — A 12-dimensional array where each index corresponds to a semitone (0-11) relative to the root, with 1 indicating the presence of a pitch class and 0 indicating its absence.

### Valid Call Patterns
```python
import mir_eval
# Example of converting a major chord quality to its bitmap
bitmap = mir_eval.chord.quality_to_bitmap("maj")
```

### LLM Instruction Prompt
- When using `quality_to_bitmap`, ensure the input is a string matching a standard chord quality recognized by `mir_eval`.
- The function returns a 12-element NumPy array; ensure your downstream logic is prepared to handle NumPy array operations.

### Prompt Snippet
```text
# Example usage:
import mir_eval
bitmap = mir_eval.chord.quality_to_bitmap("maj")
```

### Common Failure Modes
- **KeyError/ValueError**: Passing a quality string that is not defined in the `mir_eval.chord` internal quality mapping will cause the function to fail.
- **Type Mismatch**: Passing a non-string type (e.g., `None` or an integer) as the `quality` argument will result in an error during internal processing.

### Fix Code Hint
```python
import mir_eval
import numpy as np

# Ensure the quality string is valid before calling
quality_name = "maj"
try:
    bitmap = mir_eval.chord.quality_to_bitmap(quality_name)
except Exception as e:
    print(f"Invalid quality provided: {e}")
```

## API Test: `rand_index`

### Signature
```python
def rand_index(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1, beta=1.0)
```
_Source: source/mir_eval/segment.py:413_

_Source doc:_ (Non-adjusted) Rand index. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> rand_index = mir_eval.structure.rand_index(ref_intervals, ...                                            ref_labels, ...                                            est_intervals, ...                                            est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) beta : float > 0 beta value for F-measure (Default value = 1.0) Returns ------- rand_index : float > 0 Rand index

### Goal
Computes the (non-adjusted) Rand index to evaluate the similarity between reference and estimated music structure segmentations.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). The ground-truth segment intervals.
- `reference_labels`: `list`, shape=(n,). The ground-truth segment labels.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). The estimated segment intervals.
- `estimated_labels`: `list`, shape=(m,). The estimated segment labels.
- `frame_size`, default `0.1`: `float > 0`. The length in seconds of the frames used for clustering the intervals.
- `beta`, default `1.0`: `float > 0`. The beta value used for F-measure calculation.

### Input
The function expects interval and label data typically loaded via `mir_eval.io.load_labeled_intervals`. Precondition: The estimated intervals must be adjusted to match the reference timing (e.g., using `mir_eval.util.adjust_intervals`) to ensure the evaluation is performed over the same temporal range.

### Output
Returns `float > 0`: The calculated Rand index score representing the agreement between the reference and estimated segmentations.

### Valid Call Patterns
```python
# Inferred from source documentation
rand_index = mir_eval.structure.rand_index(
    ref_intervals, 
    ref_labels, 
    est_intervals, 
    est_labels
)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are `np.ndarray` objects of shape `(n, 2)` and `(m, 2)` respectively.
- Always perform interval adjustment using `mir_eval.util.adjust_intervals` before passing data to `rand_index` to prevent shape or timing mismatches.
- Use `mir_eval.io.load_labeled_intervals` to obtain the required input formats.

### Prompt Snippet
```text
# Ensure intervals are adjusted to the same range before evaluation
(ref_intervals, ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ref_labels, t_min=0)
(est_intervals, est_labels) = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
score = mir_eval.structure.rand_index(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- Passing unadjusted intervals that do not cover the same temporal range, leading to inaccurate comparisons.
- Providing `frame_size` or `beta` values less than or equal to 0, which will violate the parameter constraints.
- Mismatched lengths between intervals and labels arrays.

### Fix Code Hint
```python
# If intervals are not aligned, use adjust_intervals:
import mir_eval
ref_int, ref_lab = mir_eval.io.load_labeled_intervals('ref.lab')
est_int, est_lab = mir_eval.io.load_labeled_intervals('est.lab')
# Ensure t_max matches the reference to avoid index errors
est_int, est_lab = mir_eval.util.adjust_intervals(est_int, est_lab, t_min=0, t_max=ref_int.max())
score = mir_eval.structure.rand_index(ref_int, ref_lab, est_int, est_lab)
```

## API Test: `raw_chroma_accuracy`

### Signature
```python
def raw_chroma_accuracy(ref_voicing, ref_cent, est_voicing, est_cent, cent_tolerance=50)
```
_Source: source/mir_eval/melody.py:622_

_Source doc:_ Compute the raw chroma accuracy given two pitch (frequency) sequences in cents and matching voicing indicator sequences. The first pitch and voicing arrays are treated as the reference (truth), and the second two as the estimate (prediction).  All 4 sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> raw_chroma = mir_eval.melody.raw_chroma_accuracy(ref_v, ref_c, ...                                                  est_v, est_c) Parameters ---------- ref_voicing : np.ndarray Reference voicing array. When this array is non-binary, it is treated as a 'reference reward', as in (Bittner & Bosch, 2019) ref_cent : np.ndarray Reference pitch sequence in cents est_voicing : np.ndarray Estimated voicing array est_cent : np.ndarray Estimate pitch sequence in cents cent_tolerance : float Maximum absolute deviation in cents for a frequency value to be considered correct (Default value = 50) Returns ------- raw_chroma : float Raw chroma accuracy, the fraction of voiced frames in ref_cent for which est_cent provides a correct frequency values (within cent_tolerance cents), ignoring octave errors

### Goal
Computes the raw chroma accuracy for melody transcription by calculating the fraction of voiced reference frames where the estimated pitch matches the reference pitch within a specified tolerance, while ignoring octave errors.

### Parameters
- `ref_voicing`: `np.ndarray` representing the reference voicing state. If non-binary, it is treated as a 'reference reward' (Bittner & Bosch, 2019).
- `ref_cent`: `np.ndarray` containing the reference pitch sequence in cents.
- `est_voicing`: `np.ndarray` representing the estimated voicing state.
- `est_cent`: `np.ndarray` containing the estimated pitch sequence in cents.
- `cent_tolerance`, default `50`: `float` defining the maximum absolute deviation in cents allowed for a frequency value to be considered correct.

### Input
The function requires four `np.ndarray` inputs of identical length. Preprocessing is typically required to convert raw time-series data into cent/voicing arrays; the `mir_eval.melody.to_cent_voicing` utility is the standard method for preparing these inputs from raw time-series annotations.

### Output
Returns `float`: The raw chroma accuracy, representing the proportion of voiced reference frames where the estimate is correct within the `cent_tolerance`, disregarding octave-related discrepancies.

### Valid Call Patterns
```python
# Inferred from source documentation
raw_chroma = mir_eval.melody.raw_chroma_accuracy(ref_v, ref_c, est_v, est_c)
```

### LLM Instruction Prompt
- Always ensure all four input arrays (`ref_voicing`, `ref_cent`, `est_voicing`, `est_cent`) are of the same length before calling.
- Use `mir_eval.melody.to_cent_voicing` to prepare inputs if starting from raw time-series data.
- The function is located in the `mir_eval.melody` submodule.

### Prompt Snippet
```text
import mir_eval.melody
# Assuming ref_v, ref_c, est_v, est_c are prepared numpy arrays
accuracy = mir_eval.melody.raw_chroma_accuracy(ref_v, ref_c, est_v, est_c, cent_tolerance=50)
```

### Common Failure Modes
- **Length Mismatch:** Providing input arrays of different lengths will cause the function to fail.
- **Data Type Errors:** Passing lists instead of `np.ndarray` objects may cause internal validation errors.
- **Unvoiced Frames:** If the reference contains no voiced frames, the accuracy calculation may result in division by zero or undefined behavior depending on the implementation.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays and of equal length
ref_v = np.array(ref_v)
ref_c = np.array(ref_c)
est_v = np.array(est_v)
est_c = np.array(est_c)

if not (len(ref_v) == len(ref_c) == len(est_v) == len(est_c)):
    raise ValueError("All input arrays must have the same length.")
```

## API Test: `raw_pitch_accuracy`

### Signature
```python
def raw_pitch_accuracy(ref_voicing, ref_cent, est_voicing, est_cent, cent_tolerance=50)
```
_Source: source/mir_eval/melody.py:554_

_Source doc:_ Compute the raw pitch accuracy given two pitch (frequency) sequences in cents and matching voicing indicator sequences. The first pitch and voicing arrays are treated as the reference (truth), and the second two as the estimate (prediction).  All 4 sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> raw_pitch = mir_eval.melody.raw_pitch_accuracy(ref_v, ref_c, ...                                                est_v, est_c) Parameters ---------- ref_voicing : np.ndarray Reference voicing array. When this array is non-binary, it is treated as a 'reference reward', as in (Bittner & Bosch, 2019) ref_cent : np.ndarray Reference pitch sequence in cents est_voicing : np.ndarray Estimated voicing array est_cent : np.ndarray Estimate pitch sequence in cents cent_tolerance : float Maximum absolute deviation in cents for a frequency value to be considered correct (Default value = 50) Returns ------- raw_pitch : float Raw pitch accuracy, the fraction of voiced frames in ref_cent for which est_cent provides a correct frequency values (within cent_tolerance cents).

### Goal
Computes the raw pitch accuracy, which measures the proportion of voiced frames in the reference signal where the estimated pitch is within a specified cent tolerance.

### Parameters
- `ref_voicing`: `np.ndarray` representing the reference voicing status. If non-binary, it is treated as a 'reference reward' (Bittner & Bosch, 2019).
- `ref_cent`: `np.ndarray` representing the reference pitch sequence in cents.
- `est_voicing`: `np.ndarray` representing the estimated voicing status.
- `est_cent`: `np.ndarray` representing the estimated pitch sequence in cents.
- `cent_tolerance`, default `50`: `float` defining the maximum absolute deviation in cents allowed for a frequency value to be considered correct.

### Input
The function requires four `np.ndarray` inputs of equal length. These are typically derived from time-series data using `mir_eval.melody.to_cent_voicing` after loading raw time-series data via `mir_eval.io.load_time_series`.

### Output
Returns `float`: The raw pitch accuracy, representing the fraction of voiced frames in the reference sequence for which the estimated pitch is correct within the `cent_tolerance`.

### Valid Call Patterns
```python
# Inferred from documentation examples
raw_pitch = mir_eval.melody.raw_pitch_accuracy(ref_v, ref_c, est_v, est_c)

# With explicit tolerance
raw_pitch = mir_eval.melody.raw_pitch_accuracy(ref_v, ref_c, est_v, est_c, cent_tolerance=25.0)
```

### LLM Instruction Prompt
- Ensure all four input arrays (`ref_voicing`, `ref_cent`, `est_voicing`, `est_cent`) are of identical length before calling.
- Use `mir_eval.melody.to_cent_voicing` to prepare inputs if starting from raw time-series data.
- The function is located in the `mir_eval.melody` submodule.

### Prompt Snippet
```text
import mir_eval.melody
# Ensure ref_v, ref_c, est_v, est_c are numpy arrays of equal length
accuracy = mir_eval.melody.raw_pitch_accuracy(ref_v, ref_c, est_v, est_c, cent_tolerance=50)
```

### Common Failure Modes
- **Length Mismatch:** Providing arrays of different lengths will cause an error; ensure all four inputs are pre-processed to the same length.
- **Data Type:** Passing lists instead of `np.ndarray` may cause unexpected behavior; convert inputs to `numpy` arrays first.
- **Voicing Mismatch:** If the voicing arrays do not align with the pitch arrays, the accuracy calculation will be invalid.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays
ref_v, ref_c = np.array(ref_v), np.array(ref_c)
est_v, est_c = np.array(est_v), np.array(est_c)
# Verify lengths
assert len(ref_v) == len(ref_c) == len(est_v) == len(est_c)
```

## API Test: `reduce_extended_quality`

### Signature
```python
def reduce_extended_quality(quality)
```
_Source: source/mir_eval/chord.py:319_

_Source doc:_ Map an extended chord quality to a simpler one, moving upper voices to a set of scale degree extensions. Parameters ---------- quality : str Extended chord quality to reduce. Returns ------- base_quality : str New chord quality. extensions : set Scale degrees extensions for the quality.

### Goal
Simplifies complex chord quality strings by decomposing them into a base chord quality and a set of corresponding scale degree extensions.

### Parameters
- `quality`: `str`. The extended chord quality string (e.g., "maj7", "min9") to be reduced.

### Input
The function expects a string representing a chord quality. No specific file format is required as this is a utility function for processing chord quality labels.

### Output
Returns `(base_quality, extensions)`:
- `base_quality`: `str`. The simplified base chord quality.
- `extensions`: `set`. A set containing the scale degree extensions identified from the input quality.

### Valid Call Patterns
```python
# Inferred from signature
base_quality, extensions = mir_eval.chord.reduce_extended_quality("maj7")
```

### LLM Instruction Prompt
- When calling `reduce_extended_quality`, ensure the input is a string representing a valid chord quality.
- Always unpack the returned tuple into two variables: the base quality string and the set of extensions.
- This function is part of the `mir_eval.chord` submodule.

### Prompt Snippet
```text
from mir_eval.chord import reduce_extended_quality
base, exts = reduce_extended_quality(quality_string)
```

### Common Failure Modes
- Passing a non-string type as the `quality` argument will result in a `TypeError`.
- Passing a chord quality string that is not recognized or cannot be reduced may lead to unexpected output or errors depending on the internal implementation logic of the chord submodule.

### Fix Code Hint
```python
import mir_eval.chord
# Ensure the input is a string
quality = "maj7"
base_quality, extensions = mir_eval.chord.reduce_extended_quality(quality)
```

## API Test: `register_colormap`

### Signature
```python
def register_colormap(name, cmap)
```
_Source: source/mir_eval/display.py:117_

_Source doc:_ Handle changes to matplotlib colormap interface in 3.6.

### Goal
Registers a custom colormap within the `mir_eval` display utilities to ensure compatibility with the matplotlib colormap interface across different versions, specifically addressing changes introduced in matplotlib 3.6.

### Parameters
- `name`: The string identifier to assign to the colormap.
- `cmap`: The matplotlib colormap object to be registered.

### Input
The caller must provide a valid string for the `name` parameter and a matplotlib colormap object (typically an instance of `matplotlib.colors.Colormap`) for the `cmap` parameter. This function is intended for internal use within `mir_eval.display` to maintain consistent visualization behavior.

### Output
Returns `None`.

### Valid Call Patterns
```python
# Inferred from signature:
mir_eval.display.register_colormap(name, cmap)
```

### LLM Instruction Prompt
- When using `register_colormap`, ensure the `name` is a unique string identifier and `cmap` is a valid matplotlib colormap instance. This function should be used when extending or customizing visualization routines within the `mir_eval.display` module to ensure version-agnostic behavior with matplotlib 3.6+.

### Prompt Snippet
```text
mir_eval.display.register_colormap(name, cmap)
```

### Common Failure Modes
- **Version Mismatch:** Attempting to use this function in environments where matplotlib is significantly older than 3.6 may lead to unexpected behavior if the underlying interface assumptions are not met.
- **Invalid Object Type:** Passing an object that is not a valid matplotlib colormap to the `cmap` parameter will result in a runtime error when the colormap is subsequently accessed by plotting functions.
- **Namespace Errors:** Failing to import `mir_eval.display` before calling the function will result in a `NameError`.

### Fix Code Hint
```python
import matplotlib.pyplot as plt
import mir_eval.display

# Example: Registering a custom colormap
my_cmap = plt.get_cmap('viridis')
mir_eval.display.register_colormap('my_custom_map', my_cmap)
```

## API Test: `resample_melody_series`

### Signature
```python
def resample_melody_series(times, frequencies, voicing, times_new, kind='linear')
```
_Source: source/mir_eval/melody.py:220_

_Source doc:_ Resamples frequency and voicing time series to a new timescale. Maintains any zero ("unvoiced") values in frequencies. If ``times`` and ``times_new`` are equivalent, no resampling will be performed. Parameters ---------- times : np.ndarray Times of each frequency value frequencies : np.ndarray Array of frequency values, >= 0 voicing : np.ndarray Array which indicates voiced or unvoiced. This array may be binary or have continuous values between 0 and 1. times_new : np.ndarray Times to resample frequency and voicing sequences to kind : str kind parameter to pass to scipy.interpolate.interp1d. (Default value = 'linear') Returns ------- frequencies_resampled : np.ndarray Frequency array resampled to new timebase voicing_resampled : np.ndarray Voicing array resampled to new timebase

### Goal
Resamples melody frequency and voicing time series to a target timebase to ensure alignment between reference and estimated melody contours.

### Parameters
- `times`: `np.ndarray` representing the time stamps corresponding to each frequency and voicing value.
- `frequencies`: `np.ndarray` containing frequency values (must be >= 0).
- `voicing`: `np.ndarray` indicating voiced/unvoiced status; can be binary or continuous values between 0 and 1.
- `times_new`: `np.ndarray` representing the target time stamps for the resampled output.
- `kind`, default `'linear'`: `str` specifying the interpolation method passed to `scipy.interpolate.interp1d`.

### Input
- **Data Types:** All inputs must be `np.ndarray`.
- **Preconditions:** `frequencies` must contain non-negative values. `times` and `times_new` should be sorted in ascending order to ensure valid interpolation.
- **Compatibility:** If `times` and `times_new` are identical, the function returns the original arrays without performing interpolation.

### Output
Returns a tuple of two `np.ndarray` objects:
- `frequencies_resampled`: The frequency array interpolated to the `times_new` timebase.
- `voicing_resampled`: The voicing array interpolated to the `times_new` timebase.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example with binary voicing
times = np.arange(4) / 35.0
cents = np.array([2.0, 0.0, -1.0, 1.0])
voicing = np.array([1, 0, 1, 1])
times_new = np.linspace(0, 0.08, 9)

res_cents, res_voicing = mir_eval.melody.resample_melody_series(
    times, cents, voicing, times_new
)
```

### LLM Instruction Prompt
- When calling `resample_melody_series`, ensure all inputs are `numpy.ndarray` objects.
- Always provide `times_new` as the target timebase.
- If the voicing data is continuous (e.g., probability-based), ensure it is passed as a float array between 0 and 1.
- The function returns a tuple; always unpack it into two variables: `frequencies_resampled` and `voicing_resampled`.

### Prompt Snippet
```text
res_cents, res_voicing = mir_eval.melody.resample_melody_series(times, cents, voicing, times_new)
```

### Common Failure Modes
- **Shape Mismatch:** Passing `times`, `frequencies`, and `voicing` arrays of different lengths will raise a `ValueError` during interpolation.
- **Negative Frequencies:** Providing negative values in the `frequencies` array violates the requirement that frequencies must be >= 0.
- **Unsorted Times:** Providing `times` or `times_new` that are not monotonically increasing can lead to incorrect interpolation results or errors in `scipy.interpolate`.

### Fix Code Hint
```python
# Ensure arrays are sorted if they are not guaranteed to be
idx = np.argsort(times)
times = times[idx]
frequencies = frequencies[idx]
voicing = voicing[idx]
```

## API Test: `resample_multipitch`

### Signature
```python
def resample_multipitch(times, frequencies, target_times)
```
_Source: source/mir_eval/multipitch.py:101_

_Source doc:_ Resamples multipitch time series to a new timescale using nearest neighbor interpolation. Values in ``target_times`` outside the range of ``times`` return no pitch estimate. Parameters ---------- times : np.ndarray Array of time stamps frequencies : list of np.ndarray List of np.ndarrays of frequency values target_times : np.ndarray Array of target time stamps Returns ------- frequencies_resampled : list of numpy arrays Frequency list of lists resampled to new timebase

### Goal
Resamples a multipitch time series to a new timebase using nearest-neighbor interpolation to align frequency estimates with a target set of timestamps.

### Parameters
- `times`: `np.ndarray` — An array of time stamps corresponding to the original frequency estimates.
- `frequencies`: `list of np.ndarray` — A list where each element is a `np.ndarray` containing frequency values (pitch estimates) at the corresponding time stamp.
- `target_times`: `np.ndarray` — An array of new time stamps to which the frequency data should be resampled.

### Input
The function expects `times` and `target_times` as one-dimensional NumPy arrays. The `frequencies` input must be a list of length equal to `len(times)`, where each entry contains the pitch estimates for that specific time point. If a time point has no pitch, the corresponding entry should be an empty `np.ndarray`.

### Output
Returns `frequencies_resampled` — A list of `np.ndarray` objects, where each array contains the frequency estimates corresponding to the provided `target_times`. If a `target_time` falls outside the range of the original `times`, the result for that index will be an empty `np.ndarray`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example based on test suite usage
times = np.array([0.00, 0.01, 0.02, 0.03])
freqs = [np.array([200.0]), np.array([]), np.array([300.0, 400.0]), np.array([300.0])]
target_times = np.array([0.001, 0.01, 0.05])

resampled = mir_eval.multipitch.resample_multipitch(times, freqs, target_times)
```

### LLM Instruction Prompt
- Always ensure `times` and `target_times` are provided as `np.ndarray` objects.
- Ensure the length of `frequencies` matches the length of `times`.
- Be aware that this function uses nearest-neighbor interpolation; it does not perform linear interpolation or smoothing between frequency values.
- Handle the output as a list of arrays, noting that empty arrays represent no pitch detected at that specific target time.

### Prompt Snippet
```text
mir_eval.multipitch.resample_multipitch(times, frequencies, target_times)
```

### Common Failure Modes
- **Dimension Mismatch:** Providing a `frequencies` list that does not match the length of the `times` array will cause an index error or incorrect mapping.
- **Out-of-Bounds:** Providing `target_times` that are entirely outside the range of `times` will result in a list of empty arrays.
- **Type Errors:** Passing standard Python lists instead of `np.ndarray` for time arguments may cause failures in internal indexing logic.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays if they are not already
times = np.asarray(times)
target_times = np.asarray(target_times)

# Verify alignment
if len(times) != len(frequencies):
    raise ValueError("Length of times and frequencies must match.")
```

## API Test: `root`

### Signature
```python
def root(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1000_

_Source doc:_ Compare chords according to roots. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.root(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0], or -1 if the comparison is out of gamut.

### Goal
Evaluates the accuracy of chord root estimation by comparing the root of each estimated chord label against the corresponding reference chord label.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord labels.
- `estimated_labels`: A list of strings representing the estimated chord labels to be evaluated.

### Input
The function expects two lists of equal length containing chord labels. Before calling this function, inputs must be preprocessed to ensure they are time-aligned. Typically, this involves using `mir_eval.util.merge_labeled_intervals` to create a common timeline and `mir_eval.util.intervals_to_durations` to calculate the duration of each segment for subsequent weighted scoring.

### Output
Returns a `np.ndarray` of shape `(n,)` and `dtype=float`. Each element contains a score of `1.0` if the roots match, `0.0` if they do not, or `-1` if the comparison is out of gamut (e.g., involving non-standard or invalid chord labels).

### Valid Call Patterns
```python
# Inferred from source documentation
comparisons = mir_eval.chord.root(ref_labels, est_labels)
```

### LLM Instruction Prompt
- Always ensure `reference_labels` and `estimated_labels` are of equal length before calling `mir_eval.chord.root`.
- Use this function as part of a chord evaluation pipeline that includes interval merging and duration calculation to produce meaningful accuracy metrics.

### Prompt Snippet
```text
# Example of calculating root accuracy
comparisons = mir_eval.chord.root(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `merge_labeled_intervals` is used to synchronize inputs.
- **Invalid Labels:** Providing labels that are not recognized by the chord parser may result in `-1` values in the output array, indicating an "out of gamut" comparison.
- **Unsynchronized Intervals:** Calling this function on raw, unaligned intervals will lead to semantically incorrect evaluation results.

### Fix Code Hint
```python
# Ensure intervals are merged and aligned before calling root
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.root(ref_labels, est_labels)
```

## API Test: `rotate_bitmap_to_root`

### Signature
```python
def rotate_bitmap_to_root(bitmap, chord_root)
```
_Source: source/mir_eval/chord.py:559_

_Source doc:_ Circularly shift a relative bitmap to its absolute pitch classes. For clarity, the best explanation is an example. Given 'G:Maj', the root and quality map are as follows:: root=5 quality=[1, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 0]  # Relative chord shape After rotating to the root, the resulting bitmap becomes:: abs_quality = [0, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 1]  # G, B, and D Parameters ---------- bitmap : np.ndarray, shape=(12,) Bitmap of active notes, relative to the given root. chord_root : int Absolute pitch class number. Returns ------- bitmap : np.ndarray, shape=(12,) Absolute bitmap of active pitch classes.

### Goal
Transforms a relative chord quality bitmap into an absolute pitch class bitmap by applying a circular shift corresponding to the specified root note.

### Parameters
- `bitmap`: A 12-element NumPy array representing active notes relative to a root (e.g., `[1, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 0]` for a major triad).
- `chord_root`: An integer representing the absolute pitch class (0-11) to which the bitmap should be rotated.

### Input
The function expects a `numpy.ndarray` of shape `(12,)` for the `bitmap` and an `int` for the `chord_root`. No specific file I/O is required; the inputs must be prepared as in-memory arrays or scalars.

### Output
Returns a `numpy.ndarray` of shape `(12,)` representing the absolute bitmap of active pitch classes.

### Valid Call Patterns
```python
import numpy as np
from mir_eval.chord import rotate_bitmap_to_root

# Example: Rotate a major triad relative bitmap to root 5 (G)
relative_bitmap = np.array([1, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 0])
root = 5
absolute_bitmap = rotate_bitmap_to_root(relative_bitmap, root)
```

### LLM Instruction Prompt
- When using `rotate_bitmap_to_root`, ensure the `bitmap` is a 12-element NumPy array.
- The `chord_root` must be an integer between 0 and 11 inclusive.
- This function is typically used within chord recognition evaluation pipelines to normalize relative chord templates to absolute pitch space.

### Prompt Snippet
```text
# Rotate relative chord bitmap to absolute pitch classes
abs_bitmap = mir_eval.chord.rotate_bitmap_to_root(relative_bitmap, root_index)
```

### Common Failure Modes
- Passing a `bitmap` that does not have exactly 12 elements will result in an index error or incorrect circular shift behavior.
- Providing a `chord_root` outside the range [0, 11] may lead to unexpected results or index errors depending on the underlying NumPy implementation of the shift.
- Passing non-NumPy array types for `bitmap` may cause the function to fail if the input does not support array-like indexing or slicing.

### Fix Code Hint
```python
# Ensure bitmap is a numpy array of length 12
bitmap = np.array(bitmap)
if bitmap.shape != (12,):
    raise ValueError("Bitmap must be a 1D array of length 12.")
# Ensure root is within valid pitch class range
root = int(chord_root) % 12
```

## API Test: `rotate_bitmaps_to_roots`

### Signature
```python
def rotate_bitmaps_to_roots(bitmaps, roots)
```
_Source: source/mir_eval/chord.py:594_

_Source doc:_ Circularly shift a relative bitmaps to absolute pitch classes. See :func:`rotate_bitmap_to_root` for more information. Parameters ---------- bitmaps : np.ndarray, shape=(N, 12) Bitmap of active notes, relative to the given root. roots : np.ndarray, shape=(N,) Absolute pitch class number. Returns ------- bitmap : np.ndarray, shape=(N, 12) Absolute bitmaps of active pitch classes.

### Goal
Converts a collection of relative chord bitmaps into absolute pitch class representations by applying a circular shift based on the provided root notes.

### Parameters
- `bitmaps`: `np.ndarray`, shape=(N, 12). A matrix where each row represents a chord as a 12-element binary vector (bitmap) of active notes, defined relative to a root note.
- `roots`: `np.ndarray`, shape=(N,). An array of integers representing the absolute pitch class (0-11) for each corresponding bitmap in the `bitmaps` array.

### Input
The function expects two NumPy arrays. `bitmaps` must have 12 columns representing the chromatic scale. `roots` must contain N elements, where N matches the number of rows in `bitmaps`. Precondition: Ensure inputs are formatted as NumPy arrays before passing to the function.

### Output
Returns `np.ndarray`, shape=(N, 12). An array of absolute bitmaps where the relative note activations have been shifted to their corresponding absolute pitch class positions.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example inferred from test suite usage
ans = mir_eval.chord.rotate_bitmaps_to_roots(bitmaps, roots)
```

### LLM Instruction Prompt
- When calling `rotate_bitmaps_to_roots`, ensure `bitmaps` is a 2D NumPy array with 12 columns and `roots` is a 1D NumPy array of length N.
- The function is located in the `mir_eval.chord` submodule.
- Always verify that the number of rows in `bitmaps` matches the length of `roots`.

### Prompt Snippet
```text
mir_eval.chord.rotate_bitmaps_to_roots(bitmaps, roots)
```

### Common Failure Modes
- **Shape Mismatch:** Providing `bitmaps` and `roots` with incompatible dimensions (e.g., `bitmaps` having a different number of rows than the length of `roots`) will cause a broadcast or indexing error.
- **Invalid Data Types:** Passing lists instead of NumPy arrays may result in `AttributeError` or `TypeError` during internal array operations.
- **Out-of-Bounds Roots:** Providing root values outside the range [0, 11] may lead to unexpected circular shifts or index errors depending on the underlying NumPy implementation.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are numpy arrays
bitmaps = np.array(bitmaps)
roots = np.array(roots)

# Verify dimensions
if bitmaps.shape[0] != len(roots):
    raise ValueError("Number of bitmaps must match number of roots.")

absolute_bitmaps = mir_eval.chord.rotate_bitmaps_to_roots(bitmaps, roots)
```

## API Test: `scale_degree_to_bitmap`

### Signature
```python
def scale_degree_to_bitmap(scale_degree, modulo=False, length=BITMAP_LENGTH)
```
_Source: source/mir_eval/chord.py:211_

_Source doc:_ Create a bitmap representation of a scale degree. Note that values in the bitmap may be negative, indicating that the semitone is to be removed. Parameters ---------- scale_degree : str Spelling of a relative scale degree, e.g. 'b3', '7', '#5' modulo : bool, default=True If a scale degree exceeds the length of the bit-vector, modulo the scale degree back into the bit-vector; otherwise it is discarded. length : int, default=12 Length of the bit-vector to produce Returns ------- bitmap : np.ndarray, in [-1, 0, 1], len=`length` Bitmap representation of this scale degree.

### Goal
Converts a string-based relative scale degree spelling into a numerical bitmap representation for use in chord analysis and evaluation.

### Parameters
- `scale_degree`: A string representing the relative scale degree (e.g., 'b3', '7', '#5').
- `modulo`, default `False`: If `True`, scale degrees exceeding the bit-vector length are wrapped using modulo arithmetic; if `False`, they are discarded.
- `length`, default `BITMAP_LENGTH`: The integer length of the resulting bit-vector (default is 12).

### Input
The function expects a string input for `scale_degree`. No specific file I/O is required; it operates on in-memory string representations of musical intervals.

### Output
Returns a `np.ndarray` of length `length`, containing values in the set `{-1, 0, 1}`, where `-1` indicates a semitone to be removed, `1` indicates a semitone to be included, and `0` indicates no change.

### Valid Call Patterns
```python
import mir_eval
# Example inferred from test suite usage
bitmap = mir_eval.chord.scale_degree_to_bitmap('b3', modulo=True, length=12)
```

### LLM Instruction Prompt
- Always use the `mir_eval.chord` namespace when calling this function.
- Ensure the `scale_degree` argument is a valid string representation of a musical interval.
- When working with standard chromatic scales, ensure `length` is set to 12.

### Prompt Snippet
```text
mir_eval.chord.scale_degree_to_bitmap(scale_degree, modulo=True, length=12)
```

### Common Failure Modes
- Passing an integer instead of a string for `scale_degree` will cause a type error during parsing.
- Setting `modulo=False` with a `scale_degree` that exceeds the `length` will result in the degree being ignored, potentially leading to an empty or unexpected bitmap.
- Relying on the default `length` without verifying that `BITMAP_LENGTH` is defined in the current scope or environment.

### Fix Code Hint
```python
# Ensure the scale degree is a string and handle potential modulo requirements
import mir_eval
try:
    bitmap = mir_eval.chord.scale_degree_to_bitmap('b3', modulo=True)
except Exception as e:
    print(f"Error generating bitmap: {e}")
```

## API Test: `scale_degree_to_semitone`

### Signature
```python
def scale_degree_to_semitone(scale_degree)
```
_Source: source/mir_eval/chord.py:175_

_Source doc:_ Convert a scale degree to semitone. Parameters ---------- scale_degree : str Spelling of a relative scale degree, e.g. 'b3', '7', '#5' Returns ------- semitone : int Relative semitone of the scale degree, wrapped to a single octave Raises ------ InvalidChordException if `scale_degree` is invalid.

### Goal
Converts a string representation of a relative scale degree into its corresponding integer semitone value, normalized to a single octave.

### Parameters
- `scale_degree`: A string representing the spelling of a relative scale degree (e.g., 'b3', '7', '#5').

### Input
- **Data:** A string representing a valid musical scale degree.
- **Preconditions:** The input must be a valid scale degree string recognized by the library's chord parsing logic.

### Output
Returns `int` — The relative semitone value of the scale degree, wrapped to a single octave (0-11).

### Valid Call Patterns
```python
import mir_eval
# Example based on test suite usage
semitone = mir_eval.chord.scale_degree_to_semitone('b3')
```

### LLM Instruction Prompt
- When calling `mir_eval.chord.scale_degree_to_semitone`, ensure the input is a string representing a scale degree.
- Always wrap calls in a try-except block to handle `InvalidChordException` if the input string does not conform to expected musical notation.

### Prompt Snippet
```text
# Example of converting a scale degree to a semitone
try:
    semitone = mir_eval.chord.scale_degree_to_semitone('b3')
except mir_eval.chord.InvalidChordException:
    # Handle invalid scale degree input
    pass
```

### Common Failure Modes
- **InvalidChordException:** Raised if the provided `scale_degree` string is not a valid or recognized musical scale degree spelling.
- **Type Errors:** Passing a non-string type (e.g., an integer or list) as the `scale_degree` argument will result in a runtime error.

### Fix Code Hint
```python
# Ensure input is a string and handle potential exceptions
try:
    val = mir_eval.chord.scale_degree_to_semitone(str(input_degree))
except mir_eval.chord.InvalidChordException:
    print(f"'{input_degree}' is not a valid scale degree.")
```

## API Test: `seg`

### Signature
```python
def seg(reference_intervals, estimated_intervals)
```
_Source: source/mir_eval/chord.py:1461_

_Source doc:_ Compute the MIREX 'MeanSeg' score. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> score = mir_eval.chord.seg(ref_intervals, est_intervals) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2), dtype=float Reference chord intervals to score against. estimated_intervals : np.ndarray, shape=(m, 2), dtype=float Estimated chord intervals to score against. Returns ------- segmentation score : float Comparison score, in [0.0, 1.0], where 1.0 means perfect segmentation.

### Goal
Computes the MIREX 'MeanSeg' score to evaluate the quality of a chord segmentation by comparing estimated interval boundaries against ground-truth reference intervals.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2), dtype=float. The ground-truth start and end times for each chord segment.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2), dtype=float. The estimated start and end times for each chord segment.

### Input
- **Data Format:** Expects NumPy arrays where each row represents an interval `[start_time, end_time]`.
- **Preconditions:** Intervals should be loaded using `mir_eval.io.load_labeled_intervals`. The function assumes the intervals are chronologically ordered and non-overlapping within their respective sets.

### Output
Returns `float`: A segmentation score in the range [0.0, 1.0], where 1.0 indicates a perfect match between the reference and estimated segment boundaries.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example using test-suite pattern
ref_ivs = np.array([[0.0, 2.0], [2.0, 2.5], [2.5, 3.2]])
est_ivs = np.array([[0.0, 3.0], [3.0, 3.5]])
score = mir_eval.chord.seg(ref_ivs, est_ivs)
```

### LLM Instruction Prompt
- Always use `mir_eval.chord.seg` as the function call.
- Ensure both `reference_intervals` and `estimated_intervals` are provided as `np.ndarray` objects with shape `(n, 2)`.
- Do not attempt to pass labels to this function; it only evaluates the temporal boundaries of the segments.

### Prompt Snippet
```text
# Compute the segmentation score for chord intervals
score = mir_eval.chord.seg(reference_intervals, estimated_intervals)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays that do not have exactly two columns (start and end times) will cause a validation error.
- **Unsorted Intervals:** If the input intervals are not sorted by start time, the resulting score may not reflect the intended segmentation accuracy.
- **Empty Arrays:** Passing empty arrays may lead to undefined behavior or errors depending on the internal implementation of the interval comparison.

### Fix Code Hint
```python
# Ensure intervals are sorted by start time before evaluation
ref_ivs = ref_ivs[ref_ivs[:, 0].argsort()]
est_ivs = est_ivs[est_ivs[:, 0].argsort()]
score = mir_eval.chord.seg(ref_ivs, est_ivs)
```

## API Test: `segments`

### Signature
```python
def segments(intervals, labels, base=None, height=None, text=False, text_kw=None, ax=None, prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:180_

_Source doc:_ Plot a segmentation as a set of disjoint rectangles. Parameters ---------- intervals : np.ndarray, shape=(n, 2) segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. base : number The vertical position of the base of the rectangles. By default, this will be the bottom of the plot. height : number The height of the rectangles. By default, this will be the top of the plot (minus ``base``). .. note:: If either `base` or `height` are provided, both must be provided. text : bool If true, each segment's label is displayed in its upper-left corner text_kw : dict If ``text == True``, the properties of the text object can be specified here. See ``matplotlib.pyplot.Text`` for valid parameters ax : matplotlib.pyplot.axes An axis handle on which to draw the segmentation. If none is provided, a new set of axes is created. prop_cycle : cycle.Cycler An optional property cycle object to specify style properties. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to pass to ``matplotlib.patches.Rectangle``. Returns ------- ax : matplotlib.pyplot.axes._subplots.AxesSubplot A handle to the (possibly constructed) plot axes

### Goal
Visualizes music segmentation data as a series of disjoint rectangles on a Matplotlib axis, allowing for the inspection of structural boundaries and labels.

### Parameters
- `intervals`: A NumPy array of shape (n, 2) representing the start and end times of each segment.
- `labels`: A list of length n containing the string labels for each segment.
- `base`, default `None`: The vertical coordinate for the bottom of the rectangles; must be provided if `height` is provided.
- `height`, default `None`: The vertical height of the rectangles; must be provided if `base` is provided.
- `text`, default `False`: If `True`, renders the segment label text inside the upper-left corner of each rectangle.
- `text_kw`, default `None`: A dictionary of keyword arguments passed to the Matplotlib `Text` object when `text=True`.
- `ax`, default `None`: A Matplotlib axes object to draw on; if `None`, a new figure and axes are created.
- `prop_cycle`, default `None`: A `matplotlib.cycler.Cycler` object to define the color/style cycle for segments.
- `**kwargs`: Additional keyword arguments passed directly to `matplotlib.patches.Rectangle`.

### Input
Requires segment data typically loaded via `mir_eval.io.load_labeled_intervals`. The `intervals` must be a NumPy array of shape (n, 2) and `labels` must be a list of length n.

### Output
Returns `matplotlib.pyplot.axes._subplots.AxesSubplot`: A handle to the axes containing the rendered segmentation plot.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

intervals, labels = mir_eval.io.load_labeled_intervals("data/segment/ref00.lab")

# Basic visualization
mir_eval.display.segments(intervals, labels, text=False)

# Visualization with labels
mir_eval.display.segments(intervals, labels, text=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.display.segments` to visualize segmentation.
- Ensure `intervals` and `labels` are loaded using `mir_eval.io` utilities before passing them to the function.
- If providing `base`, you must also provide `height`.
- When `text=True`, use `text_kw` to pass styling parameters for the labels.

### Prompt Snippet
```text
mir_eval.display.segments(intervals, labels, text=True)
```

### Common Failure Modes
- **Missing Pair:** Providing `base` without `height` (or vice versa) will result in an error.
- **Mismatched Lengths:** Passing `intervals` and `labels` with different lengths will cause a runtime error during plotting.
- **Invalid Axes:** Passing an object to `ax` that is not a valid Matplotlib axes handle.

### Fix Code Hint
```python
# Ensure base and height are provided together
mir_eval.display.segments(intervals, labels, base=0, height=1)
```

## API Test: `separation`

### Signature
```python
def separation(sources, fs=22050, labels=None, alpha=0.75, ax=None, rasterized=True, edgecolors='None', shading='gouraud', prop_cycle=None, **kwargs)
```
_Source: source/mir_eval/display.py:923_

_Source doc:_ Source-separation visualization Parameters ---------- sources : np.ndarray, shape=(nsrc, nsampl) A list of waveform buffers corresponding to each source fs : number > 0 The sampling rate labels : list of strings An optional list of descriptors corresponding to each source alpha : float in [0, 1] Maximum alpha (opacity) of spectrogram values. ax : matplotlib.pyplot.axes An axis handle on which to draw the spectrograms. If none is provided, a new set of axes is created. rasterized : bool If `True`, the spectrogram is rasterized. edgecolors : str or None The color of the edges of the spectrogram patches. Set to "None" (default) to disable edge coloring. shading : str The shading method to use for the spectrogram. See `matplotlib.pyplot.pcolormesh` for valid options. prop_cycle : cycle.Cycler An optional property cycle object to specify colors for each signal. If not provided, the default property cycler will be retrieved from matplotlib. **kwargs Additional keyword arguments to ``scipy.signal.spectrogram`` Returns ------- ax The axis handle for this plot

### Goal
Visualizes the results of source separation by plotting the spectrograms of multiple audio source signals on a shared axis.

### Parameters
- `sources`: A list of waveform buffers (NumPy arrays) corresponding to each separated source.
- `fs`, default `22050`: The sampling rate of the input audio signals (must be > 0).
- `labels`, default `None`: An optional list of strings to label each source in the visualization.
- `alpha`, default `0.75`: The maximum opacity (alpha) value for the spectrogram plots.
- `ax`, default `None`: A matplotlib axes handle; if not provided, a new figure and axes are created.
- `rasterized`, default `True`: Whether to rasterize the spectrogram output for efficient rendering.
- `edgecolors`, default `'None'`: The color of the edges of the spectrogram patches; set to "None" to disable.
- `shading`, default `'gouraud'`: The shading method used for the spectrogram (see `matplotlib.pyplot.pcolormesh`).
- `prop_cycle`, default `None`: An optional `cycler.Cycler` object to define colors for each signal.
- `**kwargs`: Additional keyword arguments passed directly to `scipy.signal.spectrogram`.

### Input
Requires a list of waveform buffers as NumPy arrays. The signals must be pre-loaded (e.g., via `mir_eval.io.load_wav` or similar). Note: This module is currently marked as deprecated in the library.

### Output
Returns `ax`: The matplotlib axis handle containing the generated spectrogram visualization.

### Valid Call Patterns
```python
import mir_eval
import matplotlib.pyplot as plt

# Basic usage
mir_eval.display.separation([x0, x1, x2], fs=fs)

# Usage with labels
mir_eval.display.separation([x0, x1, x2], fs=fs, labels=["Alice", "Bob", "Carol"])
```

### LLM Instruction Prompt
- Always use `mir_eval.display.separation` as the entry point.
- Ensure `sources` is provided as a list of NumPy arrays.
- If providing labels, ensure the length of the `labels` list matches the number of arrays in `sources`.
- Note that this function is deprecated; advise users to check for library updates or alternative visualization workflows.

### Prompt Snippet
```text
mir_eval.display.separation(sources, fs=22050, labels=None, ...)
```

### Common Failure Modes
- **Deprecated Module:** The `separation` module is deprecated; users may encounter warnings or lack of support in future versions.
- **Mismatched Labels:** Providing a `labels` list that does not match the number of `sources` will cause indexing errors.
- **Invalid Sampling Rate:** Providing an `fs` value <= 0 will result in errors during spectrogram computation.
- **Missing Dependencies:** Requires `matplotlib` and `scipy` to be installed in the environment.

### Fix Code Hint
```python
# Ensure sources are loaded as arrays and fs is a positive number
import numpy as np
# If labels are used, verify length
if labels is not None and len(labels) != len(sources):
    raise ValueError("Number of labels must match number of sources.")
mir_eval.display.separation(sources, fs=fs, labels=labels)
```

## API Test: `sevenths`

### Signature
```python
def sevenths(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1236_

_Source doc:_ Compare chords along MIREX 'sevenths' rules. Chords with qualities outside [maj, maj7, 7, min, min7, N] are ignored. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.sevenths(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0], or -1 if the comparison is out of gamut.

### Goal
Evaluates chord recognition performance by comparing estimated chord labels against reference labels specifically using MIREX 'sevenths' rules, which focus on major, major-seventh, dominant-seventh, minor, minor-seventh, and no-chord qualities.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord annotations.
- `estimated_labels`: A list of strings representing the predicted chord annotations to be evaluated.

### Input
The function expects two lists of chord labels of equal length `n`. Before calling `sevenths`, inputs must be preprocessed to ensure temporal alignment. This typically involves using `mir_eval.util.merge_labeled_intervals` to create a synchronized sequence of labels corresponding to the same time intervals.

### Output
Returns a `np.ndarray` of shape `(n,)` and `dtype=float`. Each element contains a score in the range `[0.0, 1.0]` representing the accuracy of the comparison, or `-1` if the chord quality is outside the supported 'sevenths' gamut.

### Valid Call Patterns
```python
# Example derived from the source documentation
comparisons = mir_eval.chord.sevenths(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `sevenths`, ensure that `reference_labels` and `estimated_labels` are aligned lists of equal length.
- Always perform interval merging using `mir_eval.util.merge_labeled_intervals` prior to calling this function to ensure the labels correspond to the same time segments.
- Remember that this function ignores chords with qualities outside the set [maj, maj7, 7, min, min7, N].

### Prompt Snippet
```text
# Ensure labels are aligned via merge_labeled_intervals before calling
intervals, ref_labels, est_labels = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.sevenths(ref_labels, est_labels)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure inputs are synchronized via `merge_labeled_intervals`.
- **Unsupported Qualities:** Chords not matching the 'sevenths' criteria will return `-1`, which may skew downstream calculations if not handled (e.g., by filtering or using `weighted_accuracy`).
- **Unformatted Labels:** Passing raw file data without using `mir_eval.io` loaders or ensuring the strings match the expected chord syntax.

### Fix Code Hint
```python
# Filter out -1 values if calculating custom statistics
comparisons = mir_eval.chord.sevenths(ref_labels, est_labels)
valid_comparisons = comparisons[comparisons != -1]
```

## API Test: `sevenths_inv`

### Signature
```python
def sevenths_inv(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:1295_

_Source doc:_ Compare chords along MIREX 'sevenths' rules. Chords with qualities outside [maj, maj7, 7, min, min7, N] are ignored. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.sevenths_inv(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0], or -1 if the comparison is out of gamut.

### Goal
Evaluates chord estimation accuracy by comparing reference and estimated chord labels specifically against MIREX 'sevenths' quality rules.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord labels.
- `estimated_labels`: A list of strings representing the estimated chord labels to be evaluated.

### Input
- **Data:** Both inputs must be lists of chord labels of equal length `n`.
- **Preconditions:** Labels must be formatted as standard chord strings. Before calling, use `mir_eval.util.merge_labeled_intervals` to ensure reference and estimated labels are aligned over the same time intervals.
- **Gamut:** Chords with qualities outside the set `[maj, maj7, 7, min, min7, N]` are ignored during comparison.

### Output
Returns a `np.ndarray` of shape `(n,)` and `dtype=float`. Each element is a score in the range `[0.0, 1.0]` representing the accuracy of the comparison, or `-1` if the chord is outside the supported gamut.

### Valid Call Patterns
```python
# Standard usage pattern as defined in the source documentation
comparisons = mir_eval.chord.sevenths_inv(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `sevenths_inv`, ensure that the input labels have been synchronized using `mir_eval.util.merge_labeled_intervals` first.
- The function returns a score array; use `mir_eval.chord.weighted_accuracy` to aggregate these scores into a single metric if durations are available.
- Ignore any labels that result in a `-1` return value, as these indicate chords outside the MIREX 'sevenths' gamut.

### Prompt Snippet
```text
# Example of calculating sevenths_inv score
comparisons = mir_eval.chord.sevenths_inv(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing `reference_labels` and `estimated_labels` of different lengths will cause an error; ensure they are merged via `mir_eval.util.merge_labeled_intervals` first.
- **Unsupported Qualities:** Providing chords with qualities not in `[maj, maj7, 7, min, min7, N]` will result in a `-1` score for those specific indices.
- **Unsynchronized Intervals:** Calling this function on raw, unaligned interval lists will produce semantically incorrect evaluation results.

### Fix Code Hint
```python
# Ensure intervals are merged before comparison
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.sevenths_inv(ref_labels, est_labels)
```

## API Test: `sort_labeled_intervals`

### Signature
```python
def sort_labeled_intervals(intervals, labels=None)
```
_Source: source/mir_eval/util.py:171_

_Source doc:_ Sort intervals, and optionally, their corresponding labels according to start time. Parameters ---------- intervals : np.ndarray, shape=(n, 2) The input intervals labels : list, optional Labels for each interval Returns ------- intervals_sorted or (intervals_sorted, labels_sorted) Labels are only returned if provided as input

### Goal
Sorts a collection of time-based intervals (and their associated labels) chronologically by their start times to ensure deterministic processing in MIR evaluation pipelines.

### Parameters
- `intervals`: `np.ndarray`, shape=(n, 2). The input array containing n intervals, where each row represents a [start, end] time pair.
- `labels`, default `None`: `list`, optional. A list of labels corresponding to each interval. If provided, the labels are sorted in the same order as the intervals.

### Input
The function expects `intervals` to be a NumPy array of shape (n, 2). If `labels` are provided, the length of the list must match the number of rows (n) in the `intervals` array. Precondition: Ensure input data is loaded into memory as standard NumPy arrays or Python lists before calling this utility.

### Output
Returns `intervals_sorted` (a NumPy array) if `labels` is `None`. If `labels` is provided, returns a tuple `(intervals_sorted, labels_sorted)`, where `intervals_sorted` is the sorted NumPy array and `labels_sorted` is the corresponding sorted list.

### Valid Call Patterns
```python
# Sorting intervals without labels
xs = mir_eval.util.sort_labeled_intervals(x)

# Sorting intervals with corresponding labels
xs, ls = mir_eval.util.sort_labeled_intervals(x, labels)
```

### LLM Instruction Prompt
- When sorting interval data for MIR evaluation, always use `mir_eval.util.sort_labeled_intervals` to ensure chronological consistency.
- If labels are provided, ensure they are passed as a list and that the function return is unpacked into two variables.
- Do not attempt to sort intervals manually using `np.argsort` if labels are involved, as this utility handles the synchronized reordering of both structures.

### Prompt Snippet
```text
# Example of sorting intervals and labels
import mir_eval
intervals_sorted, labels_sorted = mir_eval.util.sort_labeled_intervals(intervals, labels)
```

### Common Failure Modes
- **Shape Mismatch:** Passing a `labels` list that does not have the same length as the number of rows in the `intervals` array will result in an error.
- **Incorrect Input Type:** Passing non-NumPy array types for `intervals` may cause failures in downstream metric functions that expect specific array shapes or types.
- **Ignoring Return Values:** If `labels` are passed, the function returns a tuple; failing to unpack both elements will result in the user receiving a tuple instead of the expected array.

### Fix Code Hint
```python
# Ensure labels are provided if you expect them back
if labels is not None:
    intervals, labels = mir_eval.util.sort_labeled_intervals(intervals, labels)
else:
    intervals = mir_eval.util.sort_labeled_intervals(intervals)
```

## API Test: `split`

### Signature
```python
def split(chord_label, reduce_extended_chords=False)
```
_Source: source/mir_eval/chord.py:361_

_Source doc:_ Parse a chord label into its four constituent parts: - root - quality shorthand - scale degrees - bass Note: Chords lacking quality AND interval information are major. - If a quality is specified, it is returned. - If an interval is specified WITHOUT a quality, the quality field is empty. Some examples:: 'C' -> ['C', 'maj', {}, '1'] 'G#:min(*b3,*5)/5' -> ['G#', 'min', {'*b3', '*5'}, '5'] 'A:(3)/6' -> ['A', '', {'3'}, '6'] Parameters ---------- chord_label : str A chord label. reduce_extended_chords : bool Whether to map the upper voicings of extended chords (9's, 11's, 13's) to semitone extensions. (Default value = False) Returns ------- chord_parts : list Split version of the chord label.

### Goal
Parses a standardized chord label string into its constituent components (root, quality, scale degrees, and bass) to facilitate chord-based MIR evaluation.

### Parameters
- `chord_label`: A string representing a chord label (e.g., 'C', 'G#:min(*b3,*5)/5').
- `reduce_extended_chords`, default `False`: A boolean flag; if `True`, maps upper voicings of extended chords (9ths, 11ths, 13ths) to their corresponding semitone extensions.

### Input
The function expects a string input representing a chord label. No specific file format is required as this is a string-parsing utility; however, the input must adhere to the chord labeling syntax supported by `mir_eval` (e.g., root note followed by optional quality, intervals, and bass note).

### Output
Returns a `list` containing four elements:
1. `root` (str): The root note of the chord.
2. `quality` (str): The quality shorthand (e.g., 'maj', 'min').
3. `scale_degrees` (set): A set of intervals/scale degrees.
4. `bass` (str): The bass note of the chord.

### Valid Call Patterns
```python
import mir_eval
# Basic parsing
parts = mir_eval.chord.split('C')
# Parsing with extended chord reduction
parts = mir_eval.chord.split('C:maj7', reduce_extended_chords=True)
```

### LLM Instruction Prompt
- Use `mir_eval.chord.split` to decompose chord labels into their structural components.
- Always pass the `chord_label` as a string.
- Set `reduce_extended_chords=True` only when the evaluation context requires mapping extended voicings to semitone intervals.

### Prompt Snippet
```text
# Example of splitting a chord label
root, quality, degrees, bass = mir_eval.chord.split(chord_label)
```

### Common Failure Modes
- Passing a non-string input will result in a `TypeError`.
- Providing a malformed chord label string that does not conform to the expected syntax may lead to unexpected parsing results or errors during the internal splitting logic.
- Assuming the output is a dictionary; the function returns a `list`.

### Fix Code Hint
```python
# Ensure the input is a string and handle the list return
try:
    parts = mir_eval.chord.split(my_label)
    root, quality, degrees, bass = parts
except Exception as e:
    print(f"Failed to parse chord label: {e}")
```

## API Test: `split_key_string`

### Signature
```python
def split_key_string(key)
```
_Source: source/mir_eval/key.py:93_

_Source doc:_ Split a key string (of the form, e.g. ``'C# major'``), into a tuple of ``(key, mode)`` where ``key`` is is an integer representing the semitone distance from C. Parameters ---------- key : str String representing a key. Returns ------- key : int Number of semitones above C. mode : str String representing the mode.

### Goal
Parses a human-readable key string into a standardized numerical representation of the root note and its corresponding musical mode.

### Parameters
- `key`: `str` — A string representing a musical key (e.g., `'C# major'`, `'Ab minor'`).

### Input
- **Data:** A string containing the note name (optionally with accidentals like '#' or 'b') followed by the mode (e.g., 'major' or 'minor').
- **Preconditions:** The input string must follow the format expected by the parser; otherwise, the function may fail to correctly identify the semitone distance or mode.

### Output
Returns `tuple` — A tuple containing:
- `key` (`int`): The semitone distance from C (e.g., C=0, C#=1, D=2).
- `mode` (`str`): The identified mode (e.g., `'major'` or `'minor'`).

### Valid Call Patterns
```python
# Example inferred from signature
key_int, mode_str = mir_eval.key.split_key_string('C# major')
```

### LLM Instruction Prompt
- Use `mir_eval.key.split_key_string` when converting raw key annotations into numerical formats for quantitative analysis.
- Ensure the input string is a valid key representation; the function returns a tuple of `(int, str)`.

### Prompt Snippet
```text
# To convert a key string to a semitone integer and mode:
key_val, mode = mir_eval.key.split_key_string(key_string)
```

### Common Failure Modes
- **Invalid String Format:** Passing a string that does not conform to the expected "Note Mode" format (e.g., missing a space or using unrecognized mode names) may result in incorrect parsing or `ValueError`.
- **Case Sensitivity:** Depending on the implementation, passing lowercase or non-standardized note names may lead to unexpected results; ensure inputs are normalized if necessary.

### Fix Code Hint
```python
# Ensure the input is a string and handle potential parsing errors
try:
    key_int, mode = mir_eval.key.split_key_string(my_key_str)
except ValueError:
    # Handle cases where the key string is malformed
    pass
```

## API Test: `standard_FPR`

### Signature
```python
def standard_FPR(reference_patterns, estimated_patterns, tol=1e-05)
```
_Source: source/mir_eval/pattern.py:171_

_Source doc:_ Compute the standard F1 Score, Precision and Recall. This metric checks if the prototype patterns of the reference match possible translated patterns in the prototype patterns of the estimations. Since the sizes of these prototypes must be equal, this metric is quite restrictive and it tends to be 0 in most of 2013 MIREX results. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> F, P, R = mir_eval.pattern.standard_FPR(ref_patterns, est_patterns) Parameters ---------- reference_patterns : list The reference patterns using the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format tol : float Tolerance level when comparing reference against estimation. Default parameter is the one found in the original matlab code by Tom Collins used for MIREX 2013. (Default value = 1e-5) Returns ------- f_measure : float The standard F1 Score precision : float The standard Precision recall : float The standard Recall

### Goal
Computes the standard F1-measure, precision, and recall for pattern discovery by checking if reference prototype patterns match translated versions of estimated prototype patterns.

### Parameters
- `reference_patterns`: A list of reference patterns, formatted as returned by `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns, formatted as returned by `mir_eval.io.load_patterns()`.
- `tol`, default `1e-05`: The tolerance level used for comparing reference patterns against estimated patterns; defaults to the value used in the original MATLAB code by Tom Collins for MIREX 2013.

### Input
The function requires two lists of patterns (reference and estimated) loaded via `mir_eval.io.load_patterns()`. Precondition: The sizes of the prototype patterns in both lists must be equal for the comparison to be valid.

### Output
Returns a tuple of three floats: `(f_measure, precision, recall)`.

### Valid Call Patterns
```python
import mir_eval
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
F, P, R = mir_eval.pattern.standard_FPR(ref_patterns, est_patterns)
```

### LLM Instruction Prompt
When calling `standard_FPR`, ensure that both `reference_patterns` and `estimated_patterns` have been processed through `mir_eval.io.load_patterns()`. Always assign the return value to a three-element tuple `(f_measure, precision, recall)`.

### Prompt Snippet
```text
# Example usage of standard_FPR
F, P, R = mir_eval.pattern.standard_FPR(ref_patterns, est_patterns)
```

### Common Failure Modes
- **Size Mismatch:** The function will fail or produce invalid results if the prototype sizes of the reference and estimated patterns are not equal.
- **Restrictiveness:** Due to the strict nature of the matching logic, this metric frequently returns 0, consistent with historical MIREX 2013 results.
- **Input Format:** Passing raw data that has not been formatted by `mir_eval.io.load_patterns()` will cause runtime errors.

### Fix Code Hint
```python
# Ensure patterns are loaded correctly before evaluation
import mir_eval
ref = mir_eval.io.load_patterns(ref_path)
est = mir_eval.io.load_patterns(est_path)
# Check that patterns are compatible before calling
if len(ref) == len(est):
    F, P, R = mir_eval.pattern.standard_FPR(ref, est)
```

## API Test: `tetrads`

### Signature
```python
def tetrads(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:905_

_Source doc:_ Compare chords along tetrad (root & full quality) relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.tetrads(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Evaluates the accuracy of chord estimations by comparing the root and full quality (tetrad) relationships between reference and estimated chord labels.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord annotations.
- `estimated_labels`: A list of strings representing the chord labels predicted by an MIR system.

### Input
- **Data Format:** Both inputs must be lists of equal length `n` containing chord labels (e.g., as loaded via `mir_eval.io.load_labeled_intervals`).
- **Preconditions:** Before calling `tetrads`, inputs must be synchronized in time. Use `mir_eval.util.merge_labeled_intervals` to ensure the reference and estimated labels correspond to the same time intervals.

### Output
Returns `np.ndarray` of shape `(n,)` and `dtype=float`. Each element represents a comparison score in the range `[0.0, 1.0]`, where 1.0 indicates a perfect match of the tetrad (root and quality).

### Valid Call Patterns
```python
# Standard usage pattern as derived from source documentation
comparisons = mir_eval.chord.tetrads(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `tetrads`, ensure that `reference_labels` and `estimated_labels` have been pre-processed using `mir_eval.util.merge_labeled_intervals` to ensure temporal alignment.
- The output of this function is intended to be passed to `mir_eval.chord.weighted_accuracy` along with the corresponding interval durations to compute a final score.

### Prompt Snippet
```text
# Ensure labels are aligned before comparison
comparisons = mir_eval.chord.tetrads(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `merge_labeled_intervals` is used to align the sequences.
- **Non-comparable Labels:** Using labels that cannot be parsed or compared (e.g., "X" or "N" in certain contexts) may result in invalid comparisons.
- **Missing Preprocessing:** Failing to adjust intervals to a common time range before merging will lead to inaccurate evaluation scores.

### Fix Code Hint
```python
# If labels are not aligned, use the utility functions:
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.tetrads(ref_labels, est_labels)
```

## API Test: `tetrads_inv`

### Signature
```python
def tetrads_inv(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:952_

_Source doc:_ Compare chords along tetrad (root, full quality, & bass) relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.tetrads_inv(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Evaluates the accuracy of chord estimations by comparing the root, full quality, and bass components of chord labels against a reference set.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord annotations.
- `estimated_labels`: A list of strings representing the chord annotations predicted by an MIR system.

### Input
The function expects two lists of equal length containing chord labels. Before calling this function, inputs must be synchronized in time using `mir_eval.util.merge_labeled_intervals` to ensure that each reference label corresponds to an estimated label over a specific duration.

### Output
Returns a `np.ndarray` of shape `(n,)` and type `float`, where each element is a score in the range `[0.0, 1.0]` representing the degree of match between the reference and estimated chord at that index.

### Valid Call Patterns
```python
# Standard usage after merging intervals
comparisons = mir_eval.chord.tetrads_inv(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `tetrads_inv`, ensure that `reference_labels` and `estimated_labels` have been pre-processed via `mir_eval.util.merge_labeled_intervals` so that the lists are aligned and of equal length.
- The output of this function is intended to be passed to `mir_eval.chord.weighted_accuracy` along with the corresponding interval durations to compute a final performance score.

### Prompt Snippet
```text
# Ensure labels are aligned before calling
comparisons = mir_eval.chord.tetrads_inv(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; always use `merge_labeled_intervals` to align data first.
- **Incompatible Labels:** Using labels that cannot be parsed by the chord submodule (e.g., non-standard strings) may cause unexpected behavior or errors.
- **Non-comparable labels:** If a label is not a valid chord (e.g., "X" or "N"), the function may fail to compute a valid comparison score.

### Fix Code Hint
```python
# Ensure intervals are merged and durations are calculated
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
durations = mir_eval.util.intervals_to_durations(intervals)
comparisons = mir_eval.chord.tetrads_inv(ref_labels, est_labels)
```

## API Test: `thirds`

### Signature
```python
def thirds(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:715_

_Source doc:_ Compare chords along root & third relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.thirds(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Evaluates the accuracy of chord estimations by comparing the root and third intervals of the estimated chords against the reference chords.

### Parameters
- `reference_labels`: A list of ground-truth chord labels (strings) of length `n`.
- `estimated_labels`: A list of estimated chord labels (strings) of length `n`.

### Input
The function expects two lists of chord labels of equal length. Before calling this function, inputs must be synchronized in time. Use `mir_eval.util.merge_labeled_intervals` to ensure the reference and estimated labels correspond to the same time intervals, and `mir_eval.util.adjust_intervals` to ensure temporal alignment.

### Output
Returns a `np.ndarray` of shape `(n,)` and type `float`, containing comparison scores for each interval, where values range from `0.0` (incorrect) to `1.0` (correct).

### Valid Call Patterns
```python
# Standard usage after interval merging
comparisons = mir_eval.chord.thirds(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When calling `mir_eval.chord.thirds`, ensure that `reference_labels` and `estimated_labels` are lists of equal length `n`.
- Always perform interval merging using `mir_eval.util.merge_labeled_intervals` prior to calling this function to ensure the labels are temporally aligned.
- The output is a NumPy array; use `mir_eval.chord.weighted_accuracy` with the corresponding interval durations to compute a final scalar score.

### Prompt Snippet
```text
comparisons = mir_eval.chord.thirds(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `merge_labeled_intervals` is used to align the inputs.
- **Non-comparable Labels:** Providing labels that cannot be parsed or compared (e.g., "N" or "X" in certain contexts) may result in invalid comparisons.
- **Unsynchronized Intervals:** Calling the function on raw, unmerged intervals will lead to semantically meaningless scores.

### Fix Code Hint
```python
# Ensure labels are aligned before scoring
intervals, ref_labels, est_labels = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.thirds(ref_labels, est_labels)
```

## API Test: `thirds_inv`

### Signature
```python
def thirds_inv(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:762_

_Source doc:_ Score chords along root, third, & bass relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.thirds_inv(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Evaluates the accuracy of chord estimations by comparing the root, third, and bass relationships between reference and estimated chord labels.

### Parameters
- `reference_labels`: A list of ground-truth chord labels (strings) of length `n`.
- `estimated_labels`: A list of estimated chord labels (strings) of length `n`.

### Input
- **Data:** Requires two lists of chord labels of equal length.
- **Preconditions:** Labels must be formatted as standard MIR chord strings. Before calling, inputs should be synchronized using `mir_eval.util.merge_labeled_intervals` to ensure the reference and estimated labels correspond to the same time intervals.

### Output
- Returns a `np.ndarray` of shape `(n,)` containing float scores in the range `[0.0, 1.0]`, representing the comparison result for each chord interval.

### Valid Call Patterns
```python
# Standard usage after merging intervals
comparisons = mir_eval.chord.thirds_inv(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `thirds_inv`, ensure that `reference_labels` and `estimated_labels` have been pre-processed to have identical lengths, typically by using `mir_eval.util.merge_labeled_intervals`.
- The function returns a per-interval score; use `mir_eval.chord.weighted_accuracy` with the corresponding interval durations to compute a final aggregate metric.

### Prompt Snippet
```text
# Example of computing chord thirds inversion accuracy
comparisons = mir_eval.chord.thirds_inv(ref_labels, est_labels)
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `merge_labeled_intervals` is called first.
- **Incompatible Labels:** Passing non-standard chord strings or labels that cannot be parsed by the chord submodule will cause the metric to fail or return invalid comparisons.
- **Non-comparable labels:** Attempting to compare labels that are not valid chord representations (e.g., "N" or "X" in certain contexts) may trigger internal validation errors.

### Fix Code Hint
```python
# Ensure intervals are merged and durations are calculated before scoring
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels)
durations = mir_eval.util.intervals_to_durations(intervals)
comparisons = mir_eval.chord.thirds_inv(ref_labels, est_labels)
```

## API Test: `three_layer_FPR`

### Signature
```python
def three_layer_FPR(reference_patterns, estimated_patterns)
```
_Source: source/mir_eval/pattern.py:382_

_Source doc:_ Three Layer F1 Score, Precision and Recall. As described by Meridith. Examples -------- >>> ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt") >>> est_patterns = mir_eval.io.load_patterns("est_pattern.txt") >>> F, P, R = mir_eval.pattern.three_layer_FPR(ref_patterns, ...                                            est_patterns) Parameters ---------- reference_patterns : list The reference patterns in the format returned by :func:`mir_eval.io.load_patterns()` estimated_patterns : list The estimated patterns in the same format Returns ------- f_measure : float The three-layer F1 Score precision : float The three-layer Precision recall : float The three-layer Recall

### Goal
Computes the three-layer F-measure, precision, and recall for pattern discovery evaluation as defined by Meridith.

### Parameters
- `reference_patterns`: A list of ground-truth patterns in the format returned by `mir_eval.io.load_patterns()`.
- `estimated_patterns`: A list of estimated patterns in the same format as the reference patterns.

### Input
The function expects two lists of patterns. These lists must be pre-loaded using `mir_eval.io.load_patterns()`. Ensure that the input files are formatted correctly for the pattern discovery task before loading.

### Output
Returns a tuple of three floats: `(f_measure, precision, recall)`, representing the three-layer F1 score, precision, and recall respectively.

### Valid Call Patterns
```python
import mir_eval
ref_patterns = mir_eval.io.load_patterns("ref_pattern.txt")
est_patterns = mir_eval.io.load_patterns("est_pattern.txt")
F, P, R = mir_eval.pattern.three_layer_FPR(ref_patterns, est_patterns)
```

### LLM Instruction Prompt
When using `three_layer_FPR`, ensure that both `reference_patterns` and `estimated_patterns` are loaded via `mir_eval.io.load_patterns()` to guarantee the internal data structure matches the expected list format. The function returns a tuple of three floats; always unpack these into three distinct variables.

### Prompt Snippet
```text
# Example usage of three_layer_FPR
f_score, precision, recall = mir_eval.pattern.three_layer_FPR(ref_data, est_data)
```

### Common Failure Modes
- **Format Mismatch:** Passing raw file paths or incorrectly structured lists instead of the output from `mir_eval.io.load_patterns()` will cause the function to fail during internal processing.
- **Empty Inputs:** Providing empty lists may result in undefined behavior or division by zero errors depending on the internal implementation of the F-measure calculation.
- **Missing Dependencies:** Ensure `numpy` is installed, as the underlying calculations rely on scientific Python array operations.

### Fix Code Hint
```python
# Ensure data is loaded correctly before evaluation
import mir_eval
try:
    ref = mir_eval.io.load_patterns("ref.txt")
    est = mir_eval.io.load_patterns("est.txt")
    f, p, r = mir_eval.pattern.three_layer_FPR(ref, est)
except Exception as e:
    print(f"Evaluation failed: {e}")
```

## API Test: `ticker_notes`

### Signature
```python
def ticker_notes(ax=None)
```
_Source: source/mir_eval/display.py:1077_

_Source doc:_ Set the y-axis of the given axes to MIDI notes Parameters ---------- ax : matplotlib.pyplot.axes The axes handle to apply the ticker. By default, uses the current axes handle.

### Goal
Configures the y-axis of a Matplotlib plot to display labels corresponding to MIDI note numbers, facilitating the visual inspection of pitch-based MIR data.

### Parameters
- `ax`, default `None`: A `matplotlib.pyplot.axes` object. If `None`, the function automatically targets the current active axes handle.

### Input
- **Preconditions:** The caller must have an active Matplotlib figure and axes where the y-axis data represents MIDI note values (e.g., 0–127). This function is typically called after plotting pitch contours or note events using `mir_eval.display.pitch` with the `midi=True` flag.

### Output
Returns `None`. The function modifies the provided or current `matplotlib.pyplot.axes` object in-place by updating the y-axis tick labels.

### Valid Call Patterns
```python
import matplotlib.pyplot as plt
import mir_eval

# Using the current axes handle
plt.figure()
mir_eval.display.ticker_notes()

# Using a specific axes handle
fig, ax = plt.subplots()
mir_eval.display.ticker_notes(ax=ax)
```

### LLM Instruction Prompt
- When generating code for pitch visualization, always ensure the y-axis data is scaled to MIDI note numbers before calling `ticker_notes`.
- If multiple subplots are present, explicitly pass the `ax` argument to `ticker_notes` to ensure the correct axis is labeled.

### Prompt Snippet
```text
# Example of applying MIDI note tickers to a plot
mir_eval.display.ticker_notes(ax=my_axes_handle)
```

### Common Failure Modes
- **Incorrect Data Scaling:** Calling `ticker_notes` on a plot where the y-axis represents frequency in Hertz (Hz) rather than MIDI note numbers will result in misleading axis labels.
- **Missing Axes:** Calling the function before any Matplotlib axes have been initialized or when no figure is active will raise a `matplotlib` error.
- **Overlapping Labels:** If the plot range is too small or the figure size is insufficient, MIDI note labels may overlap; adjust the figure size or axis limits accordingly.

### Fix Code Hint
```python
# Ensure data is in MIDI format before labeling
mir_eval.display.pitch(times, freqs, midi=True)
mir_eval.display.ticker_notes()
```

## API Test: `ticker_pitch`

### Signature
```python
def ticker_pitch(ax=None)
```
_Source: source/mir_eval/display.py:1095_

_Source doc:_ Set the y-axis of the given axes to MIDI frequencies Parameters ---------- ax : matplotlib.pyplot.axes The axes handle to apply the ticker. By default, uses the current axes handle.

### Goal
Configures the y-axis of a Matplotlib plot to display MIDI note numbers instead of raw frequencies, facilitating the visual inspection of pitch-based MIR tasks.

### Parameters
- `ax`, default `None`: A `matplotlib.pyplot.axes` object. If `None`, the function automatically targets the current active axes handle.

### Input
- **Preconditions:** The caller must have an existing Matplotlib figure and axes where pitch data (typically in Hz) has been plotted.
- **Data:** The axes should contain data mapped to a frequency scale to ensure the MIDI ticker conversion is meaningful.

### Output
Returns `None`. The function modifies the provided or current `matplotlib.pyplot.axes` object in-place.

### Valid Call Patterns
```python
import matplotlib.pyplot as plt
import mir_eval

# Plotting pitch data and applying the ticker
plt.figure()
mir_eval.display.pitch(times, freqs, midi=True)
mir_eval.display.ticker_pitch()
```

### LLM Instruction Prompt
- When generating code for pitch visualization, always call `mir_eval.display.ticker_pitch()` after plotting pitch data to ensure the y-axis is human-readable in MIDI note format.
- If multiple subplots are used, explicitly pass the specific `ax` object to `ticker_pitch(ax=...)` to avoid applying the ticker to the wrong subplot.

### Prompt Snippet
```text
# Example of applying MIDI ticker to a specific axis
fig, ax = plt.subplots()
mir_eval.display.pitch(times, freqs, ax=ax, midi=True)
mir_eval.display.ticker_pitch(ax=ax)
```

### Common Failure Modes
- **Missing Matplotlib Context:** Calling `ticker_pitch()` before creating a figure or axes will result in a `matplotlib` error, as there is no active axes to modify.
- **Non-Frequency Data:** Applying this ticker to a plot that does not represent frequency data will result in a misleading y-axis scale.
- **Implicit Axis Ambiguity:** In scripts with multiple figures, relying on the default `ax=None` may cause the ticker to be applied to the incorrect plot; always prefer explicit `ax` passing in complex layouts.

### Fix Code Hint
```python
# Ensure an axes object is explicitly passed if the plot is not the current one
import matplotlib.pyplot as plt
ax = plt.gca()
mir_eval.display.ticker_pitch(ax=ax)
```

## API Test: `time_frequency`

### Signature
```python
def time_frequency(gram, frequencies, times, fs, function=np.sin, length=None, n_dec=1, threshold=0.01)
```
_Source: source/mir_eval/sonify.py:64_

_Source doc:_ Reverse synthesis of a time-frequency representation of a signal Parameters ---------- gram : np.ndarray ``gram[n, m]`` is the magnitude of ``frequencies[n]`` from ``times[m]`` to ``times[m + 1]`` Non-positive magnitudes are interpreted as silence. frequencies : np.ndarray array of size ``gram.shape[0]`` denoting the frequency (in Hz) of each row of gram times : np.ndarray, shape= ``(gram.shape[1],)`` or ``(gram.shape[1], 2)`` Either the start time (in seconds) of each column in the gram, or the time interval (in seconds) corresponding to each column. fs : int desired sampling rate of the output signal function : function function to use to synthesize notes, should be 2π-periodic length : int desired number of samples in the output signal, defaults to ``times[-1]*fs`` n_dec : int the number of decimals used to approximate each sonfied frequency. Defaults to 1 decimal place. Higher precision will be slower. threshold : float optimizes synthesis to only occur for frequencies that have a linear magnitude of at least one element in gram above the given threshold. Returns ------- output : np.ndarray synthesized version of the piano roll

### Goal
Synthesizes an audio signal from a time-frequency representation (such as a spectrogram or piano roll) to allow for auditory verification of MIR analysis results.

### Parameters
- `gram`: A 2D NumPy array where `gram[n, m]` represents the magnitude of the frequency `frequencies[n]` during the time interval defined by `times[m]`. Non-positive values are treated as silence.
- `frequencies`: A 1D NumPy array of size `gram.shape[0]` containing the frequency values in Hz for each row of the `gram`.
- `times`: A 1D or 2D NumPy array. If 1D, it represents the start time (in seconds) of each column. If 2D (shape `(gram.shape[1], 2)`), it represents the start and end time intervals for each column.
- `fs`: The desired sampling rate (in Hz) for the generated output signal.
- `function`, default `np.sin`: The periodic function used to synthesize the notes; must be 2π-periodic.
- `length`, default `None`: The total number of samples in the output signal. Defaults to `times[-1] * fs`.
- `n_dec`, default `1`: The number of decimal places used to approximate frequencies during synthesis. Higher values increase precision but decrease performance.
- `threshold`, default `0.01`: A magnitude threshold; frequencies with linear magnitudes below this value are excluded from synthesis to optimize performance.

### Input
The function requires NumPy arrays for `gram`, `frequencies`, and `times`. Preconditions include ensuring `gram` dimensions align with `frequencies` and `times`, and that `fs` is a positive integer.

### Output
Returns a 1D NumPy array representing the synthesized audio signal.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Basic synthesis
signal = mir_eval.sonify.time_frequency(
    np.random.standard_normal((100, 1000)),
    np.arange(1, 101),
    np.linspace(0, 10, 1000),
    8000,
)

# Synthesis with explicit length
signal = mir_eval.sonify.time_frequency(
    np.random.standard_normal((100, 1000)),
    np.arange(1, 101),
    np.linspace(0, 10, 1000),
    8000,
    length=8000 * 11,
)
```

### LLM Instruction Prompt
- Always use `mir_eval.sonify.time_frequency` as the entry point.
- Ensure `gram` dimensions match the provided `frequencies` and `times` arrays.
- When specifying `times` as intervals, ensure the array shape is `(gram.shape[1], 2)`.
- Use `np.sin` as the default `function` unless a specific periodic waveform is required.

### Prompt Snippet
```text
mir_eval.sonify.time_frequency(gram, frequencies, times, fs, ...)
```

### Common Failure Modes
- **Dimension Mismatch:** Providing a `gram` array whose rows or columns do not match the lengths of `frequencies` or `times` will raise a `ValueError`.
- **Performance Issues:** Setting `n_dec` to a very high value can significantly slow down synthesis for large `gram` inputs.
- **Silence:** If all values in `gram` are below the `threshold` or are non-positive, the function will return a silent signal (array of zeros).

### Fix Code Hint
```python
# Ensure gram dimensions are correct
assert gram.shape[0] == len(frequencies)
assert gram.shape[1] == len(times) if times.ndim == 1 else gram.shape[1] == times.shape[0]
```

## API Test: `tmeasure`

### Signature
```python
def tmeasure(reference_intervals_hier, estimated_intervals_hier, transitive=False, window=15.0, frame_size=0.1, beta=1.0)
```
_Source: source/mir_eval/hierarchy.py:466_

_Source doc:_ Compute the tree measures for hierarchical segment annotations. Parameters ---------- reference_intervals_hier : list of ndarray ``reference_intervals_hier[i]`` contains the segment intervals (in seconds) for the ``i`` th layer of the annotations.  Layers are ordered from top to bottom, so that the last list of intervals should be the most specific. estimated_intervals_hier : list of ndarray Like ``reference_intervals_hier`` but for the estimated annotation transitive : bool whether to compute the t-measures using transitivity or not. window : float > 0 size of the window (in seconds).  For each query frame q, result frames are only counted within q +- window. frame_size : float > 0 length (in seconds) of frames.  The frame size cannot be longer than the window. beta : float > 0 beta parameter for the F-measure. Returns ------- t_precision : number [0, 1] T-measure Precision t_recall : number [0, 1] T-measure Recall t_measure : number [0, 1] F-beta measure for ``(t_precision, t_recall)`` Raises ------ ValueError If either of the input hierarchies are inconsistent If the input hierarchies have different time durations If ``frame_size > window`` or ``frame_size <= 0``

### Goal
Computes hierarchical segment evaluation metrics (T-measure) by comparing the structural alignment of estimated segment hierarchies against a reference hierarchy.

### Parameters
- `reference_intervals_hier`: A list of `np.ndarray` objects, where each array contains segment intervals (in seconds) for a specific layer of the hierarchy, ordered from top (coarse) to bottom (fine).
- `estimated_intervals_hier`: A list of `np.ndarray` objects representing the estimated segment hierarchy, following the same structure and ordering as the reference.
- `transitive`, default `False`: If `True`, computes the T-measures using transitivity across the hierarchy levels.
- `window`, default `15.0`: The tolerance window in seconds; result frames are only counted within `q ± window` for a query frame `q`.
- `frame_size`, default `0.1`: The length of frames in seconds used for discretization. Must be `> 0` and `<= window`.
- `beta`, default `1.0`: The beta parameter for the F-measure calculation, determining the weighting of precision versus recall.

### Input
The inputs must be lists of `np.ndarray` where each array has shape `(N, 2)` representing start and end times. The hierarchies must be consistent (boundaries in finer layers must align with boundaries in coarser layers) and cover the same total time duration.

### Output
Returns a tuple of three floats: `(t_precision, t_recall, t_measure)`, representing the T-measure Precision, Recall, and the F-beta measure respectively.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example inferred from test suite
ref = [np.asarray([[0, 30]]), np.asarray([[0, 15], [15, 30]])]
est = [np.asarray([[0, 30]])]
t_precision, t_recall, t_measure = mir_eval.hierarchy.tmeasure(ref, est, window=15.0, frame_size=0.1)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals_hier` and `estimated_intervals_hier` are lists of `numpy.ndarray` objects.
- Ensure the hierarchy levels are ordered from coarsest to finest.
- Validate that `frame_size` is less than or equal to `window` and both are positive.
- Be aware that inconsistent hierarchies (where finer segments do not align with coarser ones) will trigger a `UserWarning`.

### Prompt Snippet
```text
# Example usage of tmeasure
import mir_eval.hierarchy
# ref and est are lists of ndarrays
scores = mir_eval.hierarchy.tmeasure(ref, est, window=15.0, frame_size=0.1)
```

### Common Failure Modes
- **Inconsistent Hierarchy:** Providing segments where boundaries in a finer layer do not align with the coarser layer will trigger a `UserWarning` and may lead to inaccurate results.
- **Duration Mismatch:** Passing hierarchies that do not span the same total time duration will raise a `ValueError`.
- **Invalid Window/Frame:** Setting `frame_size > window` or `frame_size <= 0` will raise a `ValueError`.

### Fix Code Hint
```python
# Ensure hierarchies are consistent and durations match before calling
if not np.isclose(ref[0][-1, 1], est[0][-1, 1]):
    raise ValueError("Total duration of hierarchies must match.")
```

## API Test: `to_cent_voicing`

### Signature
```python
def to_cent_voicing(ref_time, ref_freq, est_time, est_freq, est_voicing=None, ref_reward=None, base_frequency=10.0, hop=None, kind='linear')
```
_Source: source/mir_eval/melody.py:316_

_Source doc:_ Convert reference and estimated time/frequency (Hz) annotations to sampled frequency (cent)/voicing arrays. A zero frequency indicates "unvoiced". If est_voicing is not provided, a negative frequency indicates: "Predicted as unvoiced, but if it's voiced, this is the frequency estimate". If it is provided, negative frequency values are ignored, and the voicing from est_voicing is directly used. Parameters ---------- ref_time : np.ndarray Time of each reference frequency value ref_freq : np.ndarray Array of reference frequency values est_time : np.ndarray Time of each estimated frequency value est_freq : np.ndarray Array of estimated frequency values est_voicing : np.ndarray Estimate voicing confidence. Default None, which means the voicing is inferred from est_freq: - frames with frequency <= 0.0 are considered "unvoiced" - frames with frequency > 0.0 are considered "voiced" ref_reward : np.ndarray Reference voicing reward. Default None, which means all frames are weighted equally. base_frequency : float Base frequency in Hz for conversion to cents (Default value = 10.) hop : float Hop size, in seconds, to resample, default None which means use ref_time kind : str kind parameter to pass to scipy.interpolate.interp1d. (Default value = 'linear') Returns ------- ref_voicing : np.ndarray Resampled reference voicing array ref_cent : np.ndarray Resampled reference frequency (cent) array est_voicing : np.ndarray Resampled estimated voicing array est_cent : np.ndarray Resampled estimated frequency (cent) array

### Goal
Converts raw time-frequency annotations into a standardized, resampled format of voicing and cent-frequency arrays suitable for melody evaluation.

### Parameters
- `ref_time`: `np.ndarray` of time stamps corresponding to the reference frequency values.
- `ref_freq`: `np.ndarray` of reference frequency values in Hz.
- `est_time`: `np.ndarray` of time stamps corresponding to the estimated frequency values.
- `est_freq`: `np.ndarray` of estimated frequency values in Hz.
- `est_voicing`, default `None`: `np.ndarray` representing voicing confidence. If `None`, voicing is inferred from `est_freq` (<= 0.0 is unvoiced, > 0.0 is voiced).
- `ref_reward`, default `None`: `np.ndarray` representing reference voicing reward; if `None`, all frames are weighted equally.
- `base_frequency`, default `10.0`: `float` base frequency in Hz used for the conversion to cents.
- `hop`, default `None`: `float` hop size in seconds for resampling; if `None`, `ref_time` is used as the sampling grid.
- `kind`, default `'linear'`: `str` interpolation method passed to `scipy.interpolate.interp1d`.

### Input
Requires `numpy.ndarray` objects for all time and frequency series. Data should be pre-loaded using `mir_eval.io.load_time_series` or equivalent. Precondition: `ref_time` and `ref_freq` (and similarly for estimates) must have matching lengths.

### Output
Returns a tuple of four `np.ndarray` objects: `(ref_voicing, ref_cent, est_voicing, est_cent)`. These represent the resampled, aligned voicing status (boolean) and frequency (cents) for both reference and estimation.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Standard usage as verified in test suite
ref_v, ref_c, est_v, est_c = mir_eval.melody.to_cent_voicing(
    ref_time, ref_freq, est_time, est_freq
)
```

### LLM Instruction Prompt
- Always use `mir_eval.melody.to_cent_voicing` as the fully qualified name.
- Ensure all frequency inputs are in Hz and time inputs are in seconds.
- When `est_voicing` is `None`, ensure the LLM understands that `est_freq` values <= 0.0 are treated as unvoiced.

### Prompt Snippet
```text
# Example call:
ref_v, ref_c, est_v, est_c = mir_eval.melody.to_cent_voicing(ref_time, ref_freq, est_time, est_freq)
```

### Common Failure Modes
- Mismatched array lengths between `time` and `freq` inputs.
- Providing frequency values in units other than Hz (e.g., MIDI note numbers), which will result in incorrect cent calculations.
- Assuming `est_freq` can be negative without consequence; if `est_voicing` is `None`, negative values are interpreted as unvoiced, which may lead to unexpected behavior if the user intended them to be valid frequency estimates.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays and lengths match
ref_time = np.array(ref_time)
ref_freq = np.array(ref_freq)
# If lengths differ, trim or resample before calling to_cent_voicing
```

## API Test: `triads`

### Signature
```python
def triads(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:810_

_Source doc:_ Compare chords along triad (root & quality to #5) relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.triads(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- comparison_scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Evaluates the accuracy of chord estimations by comparing the root and chord quality (up to the fifth) between reference and estimated labels.

### Parameters
- `reference_labels`: A list of strings representing the ground-truth chord annotations.
- `estimated_labels`: A list of strings representing the chord annotations to be evaluated.

### Input
- **Data:** Both inputs must be lists of chord labels of equal length `n`.
- **Preconditions:** Before calling `triads`, labels must be aligned using `mir_eval.util.merge_labeled_intervals` to ensure the reference and estimated labels correspond to the same time intervals.
- **Format:** Labels should follow standard MIR chord notation (e.g., "C:maj", "N" for no chord).

### Output
Returns a `np.ndarray` of shape `(n,)` containing float scores in the range `[0.0, 1.0]`, where 1.0 indicates a perfect match of the triad components.

### Valid Call Patterns
```python
# Inferred from source documentation and test suite
comparisons = mir_eval.chord.triads(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `triads`, ensure that the input lists are of identical length.
- Always perform interval merging via `mir_eval.util.merge_labeled_intervals` prior to calling this function to ensure temporal alignment.
- Use the resulting `np.ndarray` as input for `mir_eval.chord.weighted_accuracy` to compute a final score.

### Prompt Snippet
```text
# Ensure labels are aligned before comparison
intervals, ref_labels, est_labels = mir_eval.util.merge_labeled_intervals(ref_int, ref_lab, est_int, est_lab)
comparisons = mir_eval.chord.triads(ref_labels, est_labels)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure `merge_labeled_intervals` is used first.
- **Non-comparable Labels:** Using labels that cannot be parsed or compared (e.g., "X" vs "N") may lead to unexpected behavior or errors in the comparison logic.
- **Unweighted Scores:** Using the raw output of `triads` without weighting by duration (via `weighted_accuracy`) may provide a misleading evaluation if chord durations vary significantly.

### Fix Code Hint
```python
# If lengths differ, re-align intervals
intervals, ref_labels, est_labels = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.triads(ref_labels, est_labels)
```

## API Test: `triads_inv`

### Signature
```python
def triads_inv(reference_labels, estimated_labels)
```
_Source: source/mir_eval/chord.py:857_

_Source doc:_ Score chords along triad (root, quality to #5, & bass) relationships. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> comparisons = mir_eval.chord.triads_inv(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- reference_labels : list, len=n Reference chord labels to score against. estimated_labels : list, len=n Estimated chord labels to score against. Returns ------- scores : np.ndarray, shape=(n,), dtype=float Comparison scores, in [0.0, 1.0]

### Goal
Computes a comparison score for chord labels based on triad relationships, specifically evaluating the root, quality (up to the 5th), and bass note.

### Parameters
- `reference_labels`: A list of ground-truth chord labels (strings) of length `n`.
- `estimated_labels`: A list of estimated chord labels (strings) of length `n`.

### Input
- **Data:** Requires two lists of chord labels of equal length.
- **Preconditions:** Labels must be pre-processed into aligned intervals (e.g., using `mir_eval.util.merge_labeled_intervals`) so that each index in `reference_labels` corresponds to the same time segment as the index in `estimated_labels`.
- **Format:** Labels should follow standard MIR chord notation.

### Output
Returns `np.ndarray` of shape `(n,)` and `dtype=float`. Each element represents a comparison score in the range `[0.0, 1.0]`, where 1.0 indicates a perfect match based on the triad criteria.

### Valid Call Patterns
```python
# Standard usage after interval alignment
comparisons = mir_eval.chord.triads_inv(ref_labels, est_labels)
```

### LLM Instruction Prompt
- When using `triads_inv`, ensure that `reference_labels` and `estimated_labels` are aligned by time intervals. Use `mir_eval.util.merge_labeled_intervals` to ensure the input lists are of equal length and represent the same temporal segments before calling this function.

### Prompt Snippet
```text
comparisons = mir_eval.chord.triads_inv(reference_labels, estimated_labels)
```

### Common Failure Modes
- **Length Mismatch:** Passing lists of different lengths will result in an error; ensure inputs are aligned via `merge_labeled_intervals`.
- **Non-comparable Labels:** Using labels that cannot be parsed or compared (e.g., "X" or "N" in certain contexts) may result in zero scores or validation errors.
- **Un-normalized Intervals:** Failing to use `mir_eval.util.adjust_intervals` to handle segments outside the reference range can lead to incorrect evaluation of silence or "no chord" segments.

### Fix Code Hint
```python
# Ensure alignment before scoring
(intervals, ref_labels, est_labels) = mir_eval.util.merge_labeled_intervals(
    ref_intervals, ref_labels, est_intervals, est_labels
)
comparisons = mir_eval.chord.triads_inv(ref_labels, est_labels)
```

## API Test: `trim_beats`

### Signature
```python
def trim_beats(beats, min_beat_time=5.0)
```
_Source: source/mir_eval/beat.py:56_

_Source doc:_ Remove beats before min_beat_time.  A common preprocessing step. Parameters ---------- beats : np.ndarray Array of beat times in seconds. min_beat_time : float Minimum beat time to allow (Default value = 5.) Returns ------- beats_trimmed : np.ndarray Trimmed beat array.

### Goal
`trim_beats` removes beat time annotations that occur before a specified threshold, serving as a standard preprocessing step to ignore initial non-musical or unstable segments in beat tracking evaluation.

### Parameters
- `beats`: A `np.ndarray` containing beat times in seconds.
- `min_beat_time`, default `5.0`: A `float` representing the minimum time threshold in seconds; any beat occurring before this value will be excluded from the returned array.

### Input
The function expects a `np.ndarray` of beat times. Precondition: The input array should contain numerical values representing time in seconds. If loading from a file, use `mir_eval.io.load_events` to obtain the required array format before passing it to this function.

### Output
Returns a `np.ndarray` containing only the beat times that are greater than or equal to `min_beat_time`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Using default threshold
trimmed = mir_eval.beat.trim_beats(beats)

# Using custom threshold
trimmed = mir_eval.beat.trim_beats(beats, min_beat_time=10.0)
```

### LLM Instruction Prompt
- When calling `mir_eval.beat.trim_beats`, ensure the `beats` argument is a `np.ndarray`.
- Always verify that the input array is sorted chronologically if the downstream evaluation metrics depend on sequential beat data.
- Use this function specifically for beat-tracking tasks; do not use it for segment or interval-based tasks which require different preprocessing utilities.

### Prompt Snippet
```text
# Preprocess beats by removing those before 5 seconds
trimmed_beats = mir_eval.beat.trim_beats(estimated_beats)
```

### Common Failure Modes
- **Input Type Mismatch:** Passing a list instead of a `np.ndarray` may cause errors in downstream operations that expect NumPy-specific indexing or slicing.
- **Empty Output:** If `min_beat_time` is set higher than the latest beat in the array, the function will return an empty array, which may cause downstream metric functions (like `f_measure`) to raise errors or return zero scores.
- **Unsorted Input:** While the function trims based on time, it does not re-sort the array; if the input `beats` are not sorted, the resulting array will remain unsorted.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a numpy array and sorted before trimming
beats = np.sort(np.array(beats))
trimmed_beats = mir_eval.beat.trim_beats(beats, min_beat_time=5.0)
```

## API Test: `underseg`

### Signature
```python
def underseg(reference_intervals, estimated_intervals)
```
_Source: source/mir_eval/chord.py:1435_

_Source doc:_ Compute the MIREX 'UnderSeg' score. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> score = mir_eval.chord.underseg(ref_intervals, est_intervals) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2), dtype=float Reference chord intervals to score against. estimated_intervals : np.ndarray, shape=(m, 2), dtype=float Estimated chord intervals to score against. Returns ------- undersegmentation score : float Comparison score, in [0.0, 1.0], where 1.0 means no undersegmentation.

### Goal
Computes the MIREX 'UnderSeg' score to quantify the degree of undersegmentation in estimated chord intervals relative to a ground-truth reference.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2), dtype=float. The ground-truth chord intervals.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2), dtype=float. The estimated chord intervals to be evaluated.

### Input
The function expects two NumPy arrays of shape (N, 2) and (M, 2) containing start and end times (in seconds) for chord segments. Data should be loaded using `mir_eval.io.load_labeled_intervals` or equivalent interval-list formats. Precondition: Intervals must be non-overlapping and chronologically ordered within their respective arrays.

### Output
Returns a `float` representing the undersegmentation score in the range [0.0, 1.0], where 1.0 indicates no undersegmentation (i.e., the estimated intervals align perfectly or are more granular than the reference).

### Valid Call Patterns
```python
import mir_eval
import numpy as np

ref_ivs = np.array([[0.0, 2.0], [2.0, 2.5], [2.5, 3.2]])
est_ivs = np.array([[0.0, 3.0], [3.0, 3.5]])
score = mir_eval.chord.underseg(ref_ivs, est_ivs)
```

### LLM Instruction Prompt
- Always use the `mir_eval.chord.underseg` namespace.
- Ensure inputs are `np.ndarray` objects with a shape of `(N, 2)` and `dtype=float`.
- Do not pass labels to this function; it only evaluates the temporal interval structure.

### Prompt Snippet
```text
# Calculate undersegmentation score for chord intervals
score = mir_eval.chord.underseg(reference_intervals, estimated_intervals)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays that are not `(N, 2)` will cause internal NumPy indexing errors.
- **Unsorted Intervals:** If intervals are not provided in chronological order, the score will be mathematically invalid.
- **Overlapping Intervals:** Providing overlapping intervals within a single array violates the expected input format for chord segmentation metrics.

### Fix Code Hint
```python
# Ensure intervals are sorted by start time before evaluation
ref_ivs = ref_ivs[ref_ivs[:, 0].argsort()]
est_ivs = est_ivs[est_ivs[:, 0].argsort()]
score = mir_eval.chord.underseg(ref_ivs, est_ivs)
```

## API Test: `validate`

### Signature
```python
def validate(reference_key, estimated_key)
def validate(reference_sources, estimated_sources)
def validate(reference_patterns, estimated_patterns)
def validate(reference_timestamps: np.ndarray, estimated_timestamps: np.ndarray)
def validate(reference_tempi, reference_weight, estimated_tempi)
def validate(ref_intervals, ref_pitches, ref_velocities, est_intervals, est_pitches, est_velocities)
def validate(ref_voicing, ref_cent, est_voicing, est_cent)
def validate(ref_time, ref_freqs, est_time, est_freqs)
def validate(reference_beats, estimated_beats)
def validate(reference_onsets, estimated_onsets)
def validate(reference_labels, estimated_labels)
def validate(ref_intervals, ref_pitches, est_intervals, est_pitches)
```
_Source: source/mir_eval/transcription_velocity.py:62  (+11 more definition site/overload)_

_Source doc:_ Check that the input annotations have valid time intervals, pitches, and velocities, and throws helpful errors if not. Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) ref_pitches : np.ndarray, shape=(n,) Array of reference pitch values in Hertz ref_velocities : np.ndarray, shape=(n,) Array of MIDI velocities (i.e. between 0 and 127) of reference notes est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times) est_pitches : np.ndarray, shape=(m,) Array of estimated pitch values in Hertz est_velocities : np.ndarray, shape=(m,) Array of estimated notes time intervals (onset and offset times) est_velocities : np.ndarray, shape=(m,) Array of MIDI velocities (i.e. between 0 and 127) of estimated notes

### Goal
The `validate` function ensures that input annotation arrays conform to the structural and value-range requirements necessary for reliable MIR metric computation, raising descriptive errors or warnings when data is malformed.

### Parameters
- `ref_intervals`: `np.ndarray`, shape=(n,2). Array of reference note onset and offset times.
- `ref_pitches`: `np.ndarray`, shape=(n,). Array of reference pitch values in Hertz.
- `ref_velocities`: `np.ndarray`, shape=(n,). Array of reference MIDI velocities (0–127).
- `est_intervals`: `np.ndarray`, shape=(m,2). Array of estimated note onset and offset times.
- `est_pitches`: `np.ndarray`, shape=(m,). Array of estimated pitch values in Hertz.
- `est_velocities`: `np.ndarray`, shape=(m,). Array of estimated MIDI velocities (0–127).

### Input
Inputs must be NumPy arrays. Preconditions include ensuring that intervals are ordered correctly (onset < offset) and that pitch/velocity values fall within physically or musically meaningful ranges as defined by the specific task submodule.

### Output
Returns `None`. The function performs an in-place check and raises `ValueError` or `UserWarning` if the inputs are invalid or empty.

### Valid Call Patterns
```python
# Example for transcription validation
import numpy as np
import mir_eval
ref_int = np.array([[0.0, 1.0]])
ref_pitch = np.array([440.0])
est_int = np.array([[0.1, 1.1]])
est_pitch = np.array([442.0])
mir_eval.transcription.validate(ref_int, ref_pitch, est_int, est_pitch)
```

### LLM Instruction Prompt
- Always use the specific submodule (e.g., `mir_eval.chord.validate`) rather than a generic function.
- Ensure input arrays are pre-loaded using `mir_eval.io` utilities before validation.
- If the task involves MIDI velocities, ensure they are strictly within the [0, 127] range.

### Prompt Snippet
```text
# Validate transcription data before evaluation
mir_eval.transcription.validate(ref_intervals, ref_pitches, est_intervals, est_pitches)
```

### Common Failure Modes
- **Dimension Mismatch:** Passing arrays of different lengths for reference and estimated data (e.g., `ref_intervals` vs `ref_pitches`).
- **Empty Inputs:** Passing empty arrays without handling the resulting `UserWarning`.
- **Invalid Ranges:** Providing negative frequencies or velocities outside the 0–127 range, which will trigger a `ValueError`.

### Fix Code Hint
```python
# Ensure arrays are properly shaped and non-empty before calling
if ref_intervals.shape[0] != ref_pitches.shape[0]:
    raise ValueError("Reference intervals and pitches must have the same length.")
mir_eval.transcription.validate(ref_intervals, ref_pitches, est_intervals, est_pitches)
```

## API Test: `validate_boundary`

### Signature
```python
def validate_boundary(reference_intervals, estimated_intervals, trim)
```
_Source: source/mir_eval/segment.py:86_

_Source doc:_ Check that the input annotations to a segment boundary estimation metric (i.e. one that only takes in segment intervals) look like valid segment times, and throws helpful errors if not. Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_intervals` or :func:`mir_eval.io.load_labeled_intervals`. trim : bool will the start and end events be trimmed?

### Goal
Validates that segment boundary annotations are correctly formatted as interval arrays before being passed to segment estimation metrics.

### Parameters
- `reference_intervals`: A NumPy array of shape `(n, 2)` containing the ground-truth segment intervals.
- `estimated_intervals`: A NumPy array of shape `(m, 2)` containing the estimated segment intervals.
- `trim`: A boolean indicating whether the start and end events of the segments will be trimmed during evaluation.

### Input
- **Data Types:** Must be `np.ndarray` objects.
- **Formats:** Intervals should be in the format produced by `mir_eval.io.load_intervals` or `mir_eval.io.load_labeled_intervals`.
- **Preconditions:** The arrays must contain two columns representing start and end times for each segment.

### Output
Returns `None`. The function raises an exception if the input arrays do not conform to the expected structure or contain invalid segment times.

### Valid Call Patterns
```python
import mir_eval
# Inferred from signature:
mir_eval.segment.validate_boundary(reference_intervals, estimated_intervals, trim=True)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are `np.ndarray` objects with shape `(N, 2)` before calling this function.
- Use this function as a pre-check when implementing custom segment evaluation logic to ensure input consistency.

### Prompt Snippet
```text
mir_eval.segment.validate_boundary(ref_data, est_data, trim=True)
```

### Common Failure Modes
- **Shape Mismatch:** Passing arrays that do not have exactly two columns (e.g., 1D arrays or arrays with more than 2 columns) will trigger a validation error.
- **Invalid Data Types:** Passing lists or other non-NumPy array types will cause the validation to fail.
- **Empty Arrays:** Depending on the specific metric requirements, passing empty arrays may trigger warnings or errors if the downstream metric expects at least one segment.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure inputs are properly formatted as (N, 2) arrays
ref = np.array([[0.0, 1.5], [1.5, 3.0]])
est = np.array([[0.0, 1.6], [1.6, 3.0]])

# Validate before metric computation
mir_eval.segment.validate_boundary(ref, est, trim=True)
```

## API Test: `validate_chord_label`

### Signature
```python
def validate_chord_label(chord_label)
```
_Source: source/mir_eval/chord.py:348_

_Source doc:_ Test for well-formedness of a chord label. Parameters ---------- chord_label : str Chord label to validate.

### Goal
Validates that a given string conforms to the expected chord label syntax used within the `mir_eval.chord` submodule.

### Parameters
- `chord_label`: `str`. The chord label string to be checked for well-formedness.

### Input
- **Data:** A string representing a chord (e.g., "C:maj", "N").
- **Preconditions:** The input must be a string. This function is typically used internally by `mir_eval.chord` functions to ensure data integrity before processing, but can be called directly to verify annotation strings.

### Output
Returns `None` if the chord label is valid. If the label is malformed, it raises an exception (typically a `ValueError`).

### Valid Call Patterns
```python
import mir_eval
# Validating a standard chord label
mir_eval.chord.validate_chord_label("C:maj")

# Validating a 'no chord' label
mir_eval.chord.validate_chord_label("N")
```

### LLM Instruction Prompt
- When validating chord data, always pass the chord label string to `mir_eval.chord.validate_chord_label` to ensure it adheres to the library's expected format before performing further evaluation or metric computation.

### Prompt Snippet
```text
# Example of validating a chord label before processing
try:
    mir_eval.chord.validate_chord_label(my_chord_label)
except ValueError:
    print(f"Invalid chord label detected: {my_chord_label}")
```

### Common Failure Modes
- **Invalid Format:** Passing a string that does not follow the standard chord syntax (e.g., missing root or quality separators) will trigger a `ValueError`.
- **Type Mismatch:** Passing a non-string type (e.g., `None`, `int`, or `list`) will result in a `TypeError` during internal string processing.

### Fix Code Hint
```python
# Ensure the input is a string and conforms to expected syntax
if isinstance(chord_label, str):
    mir_eval.chord.validate_chord_label(chord_label)
else:
    raise ValueError("Chord label must be a string.")
```

## API Test: `validate_events`

### Signature
```python
def validate_events(events, max_time=30000.0)
```
_Source: source/mir_eval/util.py:763_

_Source doc:_ Check that a 1-d event location ndarray is well-formed, and raises errors if not. Parameters ---------- events : np.ndarray, shape=(n,) Array of event times max_time : float If an event is found above this time, a ValueError will be raised. (Default value = 30000.)

### Goal
Validates that a 1-dimensional NumPy array of event timestamps is well-formed and within a specified temporal boundary for use in MIR evaluation tasks.

### Parameters
- `events`: A 1-dimensional `np.ndarray` containing the event locations (e.g., beat or onset times) in seconds.
- `max_time`, default `30000.0`: A float representing the maximum allowable timestamp; any event occurring after this time will trigger a `ValueError`.

### Input
- **Data Type:** A 1-dimensional `np.ndarray` of floats.
- **Preconditions:** The array must be 1-dimensional. It is typically generated via `mir_eval.io.load_events` or similar loading utilities.

### Output
Returns `None`. If the input array is invalid (e.g., wrong dimensions or contains values exceeding `max_time`), the function raises a `ValueError`.

### Valid Call Patterns
```python
import mir_eval
# Standard validation of an event array
mir_eval.util.validate_events(events)

# Validation with a custom temporal boundary
mir_eval.util.validate_events(events, max_time=600.0)
```

### LLM Instruction Prompt
- Always use `mir_eval.util.validate_events` as the fully qualified name.
- Ensure the `events` argument is a 1-dimensional NumPy array before passing it to the function.
- Use this function to verify data integrity after loading annotations and before passing them to task-specific `evaluate()` functions.

### Prompt Snippet
```text
# Validate event array before evaluation
mir_eval.util.validate_events(estimated_events, max_time=duration)
```

### Common Failure Modes
- **Dimension Mismatch:** Passing a multi-dimensional array (e.g., an interval list with shape `(n, 2)`) will cause a validation error; use `validate_intervals` for such data instead.
- **Out-of-Bounds:** Providing timestamps that exceed the `max_time` threshold (default 30,000s) will raise a `ValueError`.
- **Non-Array Input:** Passing a list or other sequence type instead of a `np.ndarray` may cause unexpected behavior; ensure inputs are cast to `np.ndarray` first.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a 1D numpy array
events = np.array(events)
# Validate against the default or a specific duration
mir_eval.util.validate_events(events, max_time=300.0)
```

## API Test: `validate_frequencies`

### Signature
```python
def validate_frequencies(frequencies, max_freq, min_freq, allow_negatives=False)
```
_Source: source/mir_eval/util.py:794_

_Source doc:_ Check that a 1-d frequency ndarray is well-formed, and raises errors if not. Parameters ---------- frequencies : np.ndarray, shape=(n,) Array of frequency values max_freq : float If a frequency is found above this pitch, a ValueError will be raised. (Default value = 5000.) min_freq : float If a frequency is found below this pitch, a ValueError will be raised. (Default value = 20.) allow_negatives : bool Whether or not to allow negative frequency values.

### Goal
Validates that a 1-dimensional NumPy array of frequency values conforms to specified range constraints to ensure data integrity before processing in MIR tasks.

### Parameters
- `frequencies`: A 1-dimensional `np.ndarray` containing the frequency values to be validated.
- `max_freq`: A `float` representing the upper bound for valid frequencies; values exceeding this will trigger a `ValueError`.
- `min_freq`: A `float` representing the lower bound for valid frequencies; values below this will trigger a `ValueError`.
- `allow_negatives`, default `False`: A `bool` indicating whether negative frequency values are permitted in the input array.

### Input
The function expects a 1-dimensional `np.ndarray`. Preconditions include ensuring the array is numeric and correctly shaped. This utility is typically used as a guard clause within custom metric implementations or preprocessing pipelines to verify that pitch or frequency data is within physically meaningful bounds for the specific audio task.

### Output
Returns `None`. If the input array is valid, the function completes silently; otherwise, it raises a `ValueError`.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Standard validation
freqs = np.array([440.0, 880.0])
mir_eval.util.validate_frequencies(freqs, 5000, 20, allow_negatives=False)

# Validation allowing negative values
freqs_with_neg = np.array([-10.0, 440.0])
mir_eval.util.validate_frequencies(freqs_with_neg, 5000, 20, allow_negatives=True)
```

### LLM Instruction Prompt
- Always use `mir_eval.util.validate_frequencies` as the fully qualified name.
- Ensure the `frequencies` argument is a 1-dimensional NumPy array.
- Explicitly define `max_freq` and `min_freq` as floats, as they do not have default values in the signature.
- Set `allow_negatives` to `True` only if the specific MIR task (e.g., certain pitch tracking algorithms) explicitly supports negative frequency representations.

### Prompt Snippet
```text
mir_eval.util.validate_frequencies(frequencies, max_freq, min_freq, allow_negatives=False)
```

### Common Failure Modes
- **Dimension Mismatch:** Passing a multi-dimensional array instead of a 1-d `np.ndarray` will cause validation to fail or raise an error during internal processing.
- **Type Errors:** Passing lists instead of NumPy arrays may cause unexpected behavior; ensure inputs are cast to `np.ndarray` first.
- **Range Violations:** Providing frequencies outside the `[min_freq, max_freq]` range will raise a `ValueError`, which is the intended behavior for catching malformed data.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure input is a 1-d array before validation
data = np.array(my_frequency_list).flatten()
mir_eval.util.validate_frequencies(data, max_freq=5000.0, min_freq=20.0)
```

## API Test: `validate_hier_intervals`

### Signature
```python
def validate_hier_intervals(intervals_hier)
```
_Source: source/mir_eval/hierarchy.py:431_

_Source doc:_ Validate a hierarchical segment annotation. Parameters ---------- intervals_hier : ordered list of segmentations Raises ------ ValueError If any segmentation does not span the full duration of the top-level segmentation. If any segmentation does not start at 0.

### Goal
Validates the structural integrity of hierarchical segment annotations to ensure they conform to expected temporal constraints for MIR evaluation.

### Parameters
- `intervals_hier`: An ordered list of segmentations, where each segmentation represents a level in the hierarchy.

### Input
- **Data:** The input must be an ordered list of segmentations.
- **Preconditions:** Each segmentation within the list must start at 0 and span the full duration of the top-level segmentation.

### Output
Returns `None` if the validation passes. If the input violates the structural constraints, it raises a `ValueError`.

### Valid Call Patterns
```python
# Inferred from signature:
mir_eval.hierarchy.validate_hier_intervals(intervals_hier)
```

### LLM Instruction Prompt
- When calling `validate_hier_intervals`, ensure the input `intervals_hier` is an ordered list of segmentations.
- Always verify that the data has been pre-processed to start at 0 and that all levels in the hierarchy share the same total duration as the top-level segmentation before passing it to this function.

### Prompt Snippet
```text
# Ensure hierarchical segments are valid before evaluation
mir_eval.hierarchy.validate_hier_intervals(my_hierarchical_intervals)
```

### Common Failure Modes
- **`ValueError` (Duration Mismatch):** Raised if any segmentation level in the hierarchy does not span the exact same total duration as the top-level segmentation.
- **`ValueError` (Start Time Mismatch):** Raised if any segmentation level does not begin at time 0.
- **Type Errors:** Passing a single segmentation array instead of an ordered list of segmentations will cause the validation logic to fail.

### Fix Code Hint
```python
# Ensure all segmentations start at 0 and span the same duration
# before calling validation
try:
    mir_eval.hierarchy.validate_hier_intervals(intervals_hier)
except ValueError as e:
    print(f"Invalid hierarchical structure: {e}")
```

## API Test: `validate_intervals`

### Signature
```python
def validate_intervals(intervals)
def validate_intervals(ref_intervals, est_intervals)
```
_Source: source/mir_eval/transcription.py:147  (+1 more definition site/overload)_

_Source doc:_ Check that the input annotations to a metric look like time intervals, and throws helpful errors if not. Parameters ---------- ref_intervals : np.ndarray, shape=(n,2) Array of reference notes time intervals (onset and offset times) est_intervals : np.ndarray, shape=(m,2) Array of estimated notes time intervals (onset and offset times)

### Goal
Validates that input annotation arrays conform to the expected time-interval structure (onset and offset times) required for MIR metric evaluation.

### Parameters
- `ref_intervals`: A NumPy array of shape `(n, 2)` representing reference time intervals.
- `est_intervals`: A NumPy array of shape `(m, 2)` representing estimated time intervals.

### Input
- **Data Type:** Must be `np.ndarray`.
- **Structure:** Each row must contain two values: `[onset_time, offset_time]`.
- **Preconditions:** Data should be pre-loaded using `mir_eval.io` utilities. The function assumes the input represents time-based intervals; it will raise an exception if the dimensions or data types are incompatible with interval-based evaluation.

### Output
Returns `None`. The function is used for its side effect: it raises an informative error if the input arrays do not meet the required interval format.

### Valid Call Patterns
```python
# Validating a single array of intervals
mir_eval.util.validate_intervals(intervals)

# Validating reference and estimated pairs
mir_eval.util.validate_intervals(ref_intervals, est_intervals)
```

### LLM Instruction Prompt
- Use `mir_eval.util.validate_intervals` to verify data integrity before passing arrays to metric functions.
- Always ensure inputs are `np.ndarray` objects with a shape of `(N, 2)`.
- If validating a pair of annotations, pass both `ref_intervals` and `est_intervals` to the function.

### Prompt Snippet
```text
# Ensure intervals are valid before metric computation
mir_eval.util.validate_intervals(ref_intervals, est_intervals)
```

### Common Failure Modes
- **Shape Mismatch:** Passing an array that does not have exactly 2 columns (e.g., a 1D array of event times instead of intervals) will trigger a validation error.
- **Data Type Errors:** Passing lists or other non-NumPy array types may cause the validation to fail or raise an attribute error.
- **Empty Arrays:** Depending on the specific metric, passing empty arrays may be valid, but ensure the downstream metric function supports empty inputs if `validate_intervals` passes.

### Fix Code Hint
```python
import numpy as np
import mir_eval

# Ensure your data is a 2D numpy array with 2 columns
ref_intervals = np.array(ref_data)
est_intervals = np.array(est_data)

# Validate before processing
mir_eval.util.validate_intervals(ref_intervals, est_intervals)
```

## API Test: `validate_key`

### Signature
```python
def validate_key(key)
```
_Source: source/mir_eval/key.py:47_

_Source doc:_ Check that a key is well-formatted, e.g. in the form ``'C# major'``. The Key can be 'X' if it is not possible to categorize the Key and mode can be 'other' if it can't be categorized as major or minor. Parameters ---------- key : str Key to verify

### Goal
Validates that a given key string conforms to the expected format for key evaluation metrics in `mir_eval`.

### Parameters
- `key`: A string representing the musical key to verify (e.g., `'C# major'`).

### Input
- **Data:** A string.
- **Preconditions:** The string must follow the standard key notation used in the library. The value `'X'` is accepted for uncategorized keys, and the mode may be specified as `'other'` if it cannot be categorized as major or minor.

### Output
Returns `unspecified` — The function does not return a value; it raises an exception if the input `key` is not well-formatted.

### Valid Call Patterns
```python
# Inferred from signature:
mir_eval.key.validate_key('C# major')
mir_eval.key.validate_key('X')
```

### LLM Instruction Prompt
- Use `mir_eval.key.validate_key` to verify key strings before passing them to other key-related evaluation functions.
- Ensure the input is a string.
- Handle the case where the key is unknown by using the string `'X'`.

### Prompt Snippet
```text
mir_eval.key.validate_key(key_string)
```

### Common Failure Modes
- **Invalid Format:** Passing a string that does not match the expected format (e.g., missing the mode or using unsupported notation) will trigger an error.
- **Type Mismatch:** Passing a non-string type (e.g., `None` or an integer) will result in a validation error.

### Fix Code Hint
```python
import mir_eval
# Ensure the key is formatted correctly before processing
try:
    mir_eval.key.validate_key(my_key)
except ValueError as e:
    print(f"Invalid key format: {e}")
```

## API Test: `validate_structure`

### Signature
```python
def validate_structure(reference_intervals, reference_labels, estimated_intervals, estimated_labels)
```
_Source: source/mir_eval/segment.py:121_

_Source doc:_ Check that the input annotations to a structure estimation metric (i.e. one that takes in both segment boundaries and their labels) look like valid segment times and labels, and throws helpful errors if not. Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`.

### Goal
Validates that reference and estimated segment annotations conform to the expected structure estimation format, ensuring data integrity before metric computation.

### Parameters
- `reference_intervals`: `np.ndarray`, shape=(n, 2). The ground-truth segment boundaries.
- `reference_labels`: `list`, shape=(n,). The ground-truth labels corresponding to each interval.
- `estimated_intervals`: `np.ndarray`, shape=(m, 2). The predicted segment boundaries.
- `estimated_labels`: `list`, shape=(m,). The predicted labels corresponding to each interval.

### Input
Inputs must be formatted as returned by `mir_eval.io.load_labeled_intervals`. Intervals must be provided as a NumPy array of shape (N, 2) where each row represents a start and end time. Labels must be provided as a list of length N.

### Output
Returns `None`. If the inputs are invalid, the function raises an exception with a descriptive error message.

### Valid Call Patterns
```python
# Inferred from signature:
mir_eval.segment.validate_structure(
    reference_intervals, 
    reference_labels, 
    estimated_intervals, 
    estimated_labels
)
```

### LLM Instruction Prompt
- Always use `mir_eval.segment.validate_structure` to verify inputs before passing them to structure estimation metrics.
- Ensure `reference_intervals` and `estimated_intervals` are NumPy arrays of shape (N, 2).
- Ensure `reference_labels` and `estimated_labels` are lists of length N and M respectively.

### Prompt Snippet
```text
mir_eval.segment.validate_structure(ref_int, ref_lab, est_int, est_lab)
```

### Common Failure Modes
- Passing mismatched lengths between intervals and labels (e.g., `len(intervals) != len(labels)`).
- Providing intervals that are not in the expected (N, 2) NumPy array format.
- Providing labels that are not in a list format.
- Passing empty arrays or lists when the metric expects non-empty segment data.

### Fix Code Hint
```python
# Ensure data is loaded correctly via mir_eval.io
import mir_eval
ref_int, ref_lab = mir_eval.io.load_labeled_intervals('ref.txt')
est_int, est_lab = mir_eval.io.load_labeled_intervals('est.txt')

# Validate before evaluation
mir_eval.segment.validate_structure(ref_int, ref_lab, est_int, est_lab)
```

## API Test: `validate_tempi`

### Signature
```python
def validate_tempi(tempi, reference=True)
```
_Source: source/mir_eval/tempo.py:29_

_Source doc:_ Check that there are two non-negative tempi. For a reference value, at least one tempo has to be greater than zero. Parameters ---------- tempi : np.ndarray length-2 array of tempo, in bpm reference : bool indicates a reference value

### Goal
Validates that a provided array of tempo values meets the structural and non-negativity requirements for tempo evaluation in MIR tasks.

### Parameters
- `tempi`: A `np.ndarray` representing a length-2 array of tempo values in beats per minute (BPM).
- `reference`, default `True`: A boolean flag indicating whether the input is a reference value; if `True`, the function enforces that at least one tempo must be greater than zero.

### Input
- **Data Type:** `np.ndarray` of length 2.
- **Preconditions:** The array must contain non-negative numerical values. If `reference` is `True`, the array must contain at least one value strictly greater than zero.

### Output
Returns `None` if validation succeeds. If validation fails, the function raises an exception (e.g., `ValueError` or `TypeError` depending on the input violation).

### Valid Call Patterns
```python
import numpy as np
from mir_eval.tempo import validate_tempi

# Example inferred from signature: validating a reference tempo array
tempi = np.array([120.0, 60.0])
validate_tempi(tempi, reference=True)
```

### LLM Instruction Prompt
- When calling `validate_tempi`, ensure the `tempi` argument is a NumPy array of exactly length 2.
- Always specify the `reference` boolean based on whether the data is ground truth (reference) or estimated output.
- Do not attempt to use this function for arrays of length other than 2.

### Prompt Snippet
```text
mir_eval.tempo.validate_tempi(tempi, reference=True)
```

### Common Failure Modes
- **Incorrect Array Length:** Passing an array that is not length-2 will cause a validation error.
- **Negative Values:** Providing negative tempo values will trigger a failure.
- **Zero-Tempo Reference:** If `reference=True`, passing `[0.0, 0.0]` will fail because at least one tempo must be greater than zero.
- **Type Mismatch:** Passing a list instead of a `np.ndarray` may cause issues depending on the internal implementation's reliance on NumPy-specific methods.

### Fix Code Hint
```python
import numpy as np
# Ensure input is a numpy array of length 2
tempi = np.array([float(t1), float(t2)])
validate_tempi(tempi, reference=True)
```

## API Test: `validate_voicing`

### Signature
```python
def validate_voicing(ref_voicing, est_voicing)
```
_Source: source/mir_eval/melody.py:77_

_Source doc:_ Check that voicing inputs to a metric are in the correct format. Parameters ---------- ref_voicing : np.ndarray Reference voicing array est_voicing : np.ndarray Estimated voicing array

### Goal
Validates that the provided reference and estimated voicing arrays conform to the expected format required for melody or pitch-related evaluation metrics.

### Parameters
- `ref_voicing`: `np.ndarray` representing the ground truth voicing state (typically binary indicators of voiced/unvoiced segments).
- `est_voicing`: `np.ndarray` representing the estimated voicing state to be compared against the reference.

### Input
- **Data Types:** Both inputs must be `np.ndarray` objects.
- **Preconditions:** The arrays should contain voicing information (e.g., 1 for voiced, 0 for unvoiced) corresponding to the time steps of the melody or pitch contour being evaluated.

### Output
Returns `None`. If the inputs are valid, the function completes silently; otherwise, it raises an exception (e.g., `ValueError` or `TypeError`) indicating a format mismatch.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example inferred from signature
ref = np.array([1, 1, 0, 0])
est = np.array([1, 0, 0, 1])
mir_eval.melody.validate_voicing(ref, est)
```

### LLM Instruction Prompt
- When calling `validate_voicing`, ensure both `ref_voicing` and `est_voicing` are passed as `numpy.ndarray` objects.
- This function is intended for internal validation within melody or pitch evaluation workflows; ensure it is called before passing voicing data to metric functions that require strict format adherence.

### Prompt Snippet
```text
mir_eval.melody.validate_voicing(ref_voicing, est_voicing)
```

### Common Failure Modes
- **Type Mismatch:** Passing lists instead of `np.ndarray` objects will cause the validation to fail.
- **Shape Mismatch:** If `ref_voicing` and `est_voicing` have different lengths, the function will raise an error, as they must represent the same number of time steps.
- **Invalid Values:** Providing arrays containing values other than the expected voicing indicators (e.g., non-binary values where binary is expected) may trigger validation errors depending on the specific implementation requirements.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are numpy arrays and have matching shapes
ref_voicing = np.asarray(ref_voicing)
est_voicing = np.asarray(est_voicing)
mir_eval.melody.validate_voicing(ref_voicing, est_voicing)
```

## API Test: `vmeasure`

### Signature
```python
def vmeasure(reference_intervals, reference_labels, estimated_intervals, estimated_labels, frame_size=0.1, beta=1.0)
```
_Source: source/mir_eval/segment.py:1100_

_Source doc:_ Frame-clustering segmentation: v-measure Computes cross-entropy of cluster assignment, normalized by the marginal-entropy. This is equivalent to `nce(..., marginal=True)`. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> # Trim or pad the estimate to match reference timing >>> (ref_intervals, ...  ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ...                                               ref_labels, ...                                               t_min=0) >>> (est_intervals, ...  est_labels) = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, t_min=0, t_max=ref_intervals.max()) >>> V_precision, V_recall, V_F = mir_eval.structure.vmeasure(ref_intervals, ...                                                          ref_labels, ...                                                          est_intervals, ...                                                          est_labels) Parameters ---------- reference_intervals : np.ndarray, shape=(n, 2) reference segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. reference_labels : list, shape=(n,) reference segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_intervals : np.ndarray, shape=(m, 2) estimated segment intervals, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. estimated_labels : list, shape=(m,) estimated segment labels, in the format returned by :func:`mir_eval.io.load_labeled_intervals`. frame_size : float > 0 length (in seconds) of frames for clustering (Default value = 0.1) beta : float > 0 beta for F-measure (Default value = 1.0) Returns ------- V_precision Over-clustering score: ``1 - H(y_est | y_ref) / H(y_est)`` If `|y_est|==1`, then `V_precision` will be 0. V_recall Under-clustering score: ``1 - H(y_ref | y_est) / H(y_ref)`` If `|y_ref|==1`, then `V_recall` will be 0. V_F

### Goal
Computes the V-measure for music segmentation by evaluating the cross-entropy of cluster assignments normalized by marginal entropy, providing a balanced metric for over-clustering and under-clustering.

### Parameters
- `reference_intervals`: `np.ndarray` of shape `(n, 2)` containing the ground truth segment start and end times.
- `reference_labels`: `list` of length `n` containing the ground truth labels for each segment.
- `estimated_intervals`: `np.ndarray` of shape `(m, 2)` containing the estimated segment start and end times.
- `estimated_labels`: `list` of length `m` containing the estimated labels for each segment.
- `frame_size`, default `0.1`: `float > 0` representing the length in seconds of the frames used for clustering.
- `beta`, default `1.0`: `float > 0` representing the beta parameter used for the F-measure calculation.

### Input
Inputs must be formatted as labeled intervals (start/end times and associated labels). Before calling, it is highly recommended to use `mir_eval.util.adjust_intervals` to ensure the estimated intervals are trimmed or padded to match the reference timing, as V-measure is sensitive to temporal alignment.

### Output
Returns a tuple `(V_precision, V_recall, V_F)`:
- `V_precision`: The over-clustering score.
- `V_recall`: The under-clustering score.
- `V_F`: The weighted F-measure of the V-precision and V-recall.

### Valid Call Patterns
```python
# Inferred from documentation examples
V_precision, V_recall, V_F = mir_eval.structure.vmeasure(
    ref_intervals, ref_labels, est_intervals, est_labels
)
```

### LLM Instruction Prompt
- Always ensure `reference_intervals` and `estimated_intervals` are pre-processed using `mir_eval.util.adjust_intervals` to align the time boundaries before passing them to `vmeasure`.
- Use the `mir_eval.structure` namespace for this function.

### Prompt Snippet
```text
V_precision, V_recall, V_F = mir_eval.structure.vmeasure(ref_intervals, ref_labels, est_intervals, est_labels)
```

### Common Failure Modes
- **Temporal Mismatch:** Failing to align `estimated_intervals` with `reference_intervals` using `mir_eval.util.adjust_intervals` will lead to inaccurate clustering metrics.
- **Invalid Frame Size:** Providing a `frame_size <= 0` will raise an error.
- **Single Label Input:** If the reference or estimate contains only one unique label, the corresponding precision or recall score will be 0.

### Fix Code Hint
```python
# Ensure alignment before evaluation
(ref_intervals, ref_labels) = mir_eval.util.adjust_intervals(ref_intervals, ref_labels, t_min=0)
(est_intervals, est_labels) = mir_eval.util.adjust_intervals(est_intervals, est_labels, t_min=0, t_max=ref_intervals.max())
V_precision, V_recall, V_F = mir_eval.structure.vmeasure(ref_intervals, ref_labels, est_intervals, est_labels)
```

## API Test: `voicing_false_alarm`

### Signature
```python
def voicing_false_alarm(ref_voicing, est_voicing)
```
_Source: source/mir_eval/melody.py:478_

_Source doc:_ Compute the voicing false alarm rates given two voicing indicator sequences, one as reference (truth) and the other as the estimate (prediction).  The sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> false_alarm = mir_eval.melody.voicing_false_alarm(ref_v, est_v) Parameters ---------- ref_voicing : np.ndarray Reference boolean voicing array est_voicing : np.ndarray Estimated boolean voicing array Returns ------- vx_false_alarm : float Voicing false alarm rate, the fraction of unvoiced frames in ref indicated as voiced in est

### Goal
Computes the voicing false alarm rate, which quantifies the proportion of unvoiced frames in the reference signal that were incorrectly identified as voiced in the estimated signal.

### Parameters
- `ref_voicing`: `np.ndarray` — A boolean array representing the ground-truth voicing state (True for voiced, False for unvoiced).
- `est_voicing`: `np.ndarray` — A boolean array representing the estimated voicing state (True for voiced, False for unvoiced).

### Input
The function requires two NumPy arrays of identical length. These arrays are typically derived from raw time-series data using `mir_eval.melody.to_cent_voicing` to ensure the reference and estimate are aligned and sampled at the same intervals.

### Output
Returns `float` — The voicing false alarm rate, calculated as the fraction of frames where `ref_voicing` is False (unvoiced) but `est_voicing` is True (voiced).

### Valid Call Patterns
```python
# Inferred from source documentation
false_alarm = mir_eval.melody.voicing_false_alarm(ref_v, est_v)
```

### LLM Instruction Prompt
- When calling `voicing_false_alarm`, ensure both `ref_voicing` and `est_voicing` are boolean NumPy arrays of the same length.
- Always preprocess raw frequency/time data using `mir_eval.melody.to_cent_voicing` before passing the resulting voicing arrays to this function to ensure temporal alignment.

### Prompt Snippet
```text
# Ensure voicing arrays are aligned and boolean
ref_v, est_v = mir_eval.melody.to_cent_voicing(ref_time, ref_freq, est_time, est_freq)
false_alarm = mir_eval.melody.voicing_false_alarm(ref_v, est_v)
```

### Common Failure Modes
- **Length Mismatch:** Passing arrays of different lengths will cause an error, as the function requires frame-by-frame comparison.
- **Non-Boolean Inputs:** Passing non-boolean arrays may lead to unexpected behavior or errors during the logical comparison of voicing states.
- **Unsynchronized Data:** Failing to resample or align the reference and estimate time series before extraction will result in meaningless false alarm rates.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are boolean
ref_voicing = ref_voicing.astype(bool)
est_voicing = est_voicing.astype(bool)
# Check lengths
if len(ref_voicing) != len(est_voicing):
    raise ValueError("ref_voicing and est_voicing must have the same length.")
```

## API Test: `voicing_measures`

### Signature
```python
def voicing_measures(ref_voicing, est_voicing)
```
_Source: source/mir_eval/melody.py:515_

_Source doc:_ Compute the voicing recall and false alarm rates given two voicing indicator sequences, one as reference (truth) and the other as the estimate (prediction).  The sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> recall, false_alarm = mir_eval.melody.voicing_measures(ref_v, ...                                                        est_v) Parameters ---------- ref_voicing : np.ndarray Reference boolean voicing array est_voicing : np.ndarray Estimated boolean voicing array Returns ------- vx_recall : float Voicing recall rate, the fraction of voiced frames in ref indicated as voiced in est vx_false_alarm : float Voicing false alarm rate, the fraction of unvoiced frames in ref indicated as voiced in est

### Goal
Computes the voicing recall and false alarm rates to evaluate the accuracy of a melody transcription's voicing detection against a ground-truth reference.

### Parameters
- `ref_voicing`: `np.ndarray` (boolean). The reference (ground-truth) voicing indicator sequence.
- `est_voicing`: `np.ndarray` (boolean). The estimated (predicted) voicing indicator sequence.

### Input
- **Data:** Both inputs must be NumPy arrays of the same length representing boolean voicing states (e.g., 1 for voiced, 0 for unvoiced).
- **Preconditions:** The sequences must be of the same length. Typically, these are derived from raw time-frequency series using `mir_eval.melody.to_cent_voicing` to ensure temporal alignment and consistent voicing definitions.

### Output
Returns a tuple of two floats:
- `vx_recall`: The fraction of voiced frames in the reference that are correctly identified as voiced in the estimate.
- `vx_false_alarm`: The fraction of unvoiced frames in the reference that are incorrectly identified as voiced in the estimate.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example using pre-processed voicing arrays
recall, false_alarm = mir_eval.melody.voicing_measures(ref_v, est_v)
```

### LLM Instruction Prompt
- When calling `mir_eval.melody.voicing_measures`, ensure both `ref_voicing` and `est_voicing` are NumPy arrays of identical length.
- Always use the `mir_eval.melody` namespace.
- Note that this function returns a tuple of two floats: `(recall, false_alarm)`.

### Prompt Snippet
```text
recall, false_alarm = mir_eval.melody.voicing_measures(ref_voicing, est_voicing)
```

### Common Failure Modes
- **Length Mismatch:** Passing arrays of different lengths will raise a `ValueError`.
- **Empty Arrays:** Passing empty arrays will trigger `UserWarning` messages and return 0 for both metrics.
- **Non-Voiced Content:** If the reference or estimate contains no voiced frames, the function will issue a `UserWarning`.
- **Input Type:** Passing lists instead of `np.ndarray` may cause unexpected behavior; ensure inputs are cast to NumPy arrays.

### Fix Code Hint
```python
# Ensure inputs are numpy arrays and of equal length
ref_v = np.array(ref_v)
est_v = np.array(est_v)
if len(ref_v) == len(est_v):
    recall, false_alarm = mir_eval.melody.voicing_measures(ref_v, est_v)
```

## API Test: `voicing_recall`

### Signature
```python
def voicing_recall(ref_voicing, est_voicing)
```
_Source: source/mir_eval/melody.py:441_

_Source doc:_ Compute the voicing recall given two voicing indicator sequences, one as reference (truth) and the other as the estimate (prediction).  The sequences must be of the same length. Examples -------- >>> ref_time, ref_freq = mir_eval.io.load_time_series('ref.txt') >>> est_time, est_freq = mir_eval.io.load_time_series('est.txt') >>> (ref_v, ref_c, ...  est_v, est_c) = mir_eval.melody.to_cent_voicing(ref_time, ...                                                  ref_freq, ...                                                  est_time, ...                                                  est_freq) >>> recall = mir_eval.melody.voicing_recall(ref_v, est_v) Parameters ---------- ref_voicing : np.ndarray Reference boolean voicing array est_voicing : np.ndarray Estimated boolean voicing array Returns ------- vx_recall : float Voicing recall rate, the fraction of voiced frames in ref indicated as voiced in est

### Goal
Computes the voicing recall rate, which measures the proportion of voiced frames in the reference signal that are correctly identified as voiced in the estimated signal.

### Parameters
- `ref_voicing`: `np.ndarray` — A boolean array representing the ground truth voicing state (True for voiced, False for unvoiced).
- `est_voicing`: `np.ndarray` — A boolean array representing the estimated voicing state (True for voiced, False for unvoiced).

### Input
The function requires two NumPy arrays of identical length. These arrays are typically derived from raw time-series data using `mir_eval.melody.to_cent_voicing` to ensure the reference and estimate are aligned and formatted as boolean voicing indicators.

### Output
Returns `vx_recall` (float): The voicing recall rate, calculated as the fraction of frames where both the reference and estimate are voiced, divided by the total number of voiced frames in the reference.

### Valid Call Patterns
```python
import mir_eval
# Assuming ref_v and est_v are boolean arrays of the same length
recall = mir_eval.melody.voicing_recall(ref_v, est_v)
```

### LLM Instruction Prompt
- When calling `voicing_recall`, ensure that both `ref_voicing` and `est_voicing` are boolean NumPy arrays of the same length.
- Always use the `mir_eval.melody` namespace.
- If raw time-series data is provided, first convert it using `mir_eval.melody.to_cent_voicing` to obtain the required boolean voicing arrays.

### Prompt Snippet
```text
recall = mir_eval.melody.voicing_recall(ref_voicing, est_voicing)
```

### Common Failure Modes
- **Length Mismatch:** Passing arrays of different lengths will result in an error; ensure inputs are pre-processed to the same temporal resolution.
- **Non-Boolean Inputs:** Passing non-boolean arrays may lead to unexpected behavior; ensure inputs are cast to boolean or represent voicing as binary indicators.
- **Empty Arrays:** Providing empty arrays will result in a division by zero or undefined recall; ensure input arrays contain valid frame data.

### Fix Code Hint
```python
import numpy as np
# Ensure inputs are boolean and same length
ref_v = np.array(ref_v, dtype=bool)
est_v = np.array(est_v, dtype=bool)
if len(ref_v) == len(est_v):
    recall = mir_eval.melody.voicing_recall(ref_v, est_v)
```

## API Test: `weighted_accuracy`

### Signature
```python
def weighted_accuracy(comparisons, weights)
```
_Source: source/mir_eval/chord.py:647_

_Source doc:_ Compute the weighted accuracy of a list of chord comparisons. Examples -------- >>> (ref_intervals, ...  ref_labels) = mir_eval.io.load_labeled_intervals('ref.lab') >>> (est_intervals, ...  est_labels) = mir_eval.io.load_labeled_intervals('est.lab') >>> est_intervals, est_labels = mir_eval.util.adjust_intervals( ...     est_intervals, est_labels, ref_intervals.min(), ...     ref_intervals.max(), mir_eval.chord.NO_CHORD, ...     mir_eval.chord.NO_CHORD) >>> (intervals, ...  ref_labels, ...  est_labels) = mir_eval.util.merge_labeled_intervals( ...      ref_intervals, ref_labels, est_intervals, est_labels) >>> durations = mir_eval.util.intervals_to_durations(intervals) >>> # Here, we're using the "thirds" function to compare labels >>> # but any of the comparison functions would work. >>> comparisons = mir_eval.chord.thirds(ref_labels, est_labels) >>> score = mir_eval.chord.weighted_accuracy(comparisons, durations) Parameters ---------- comparisons : np.ndarray List of chord comparison scores, in [0, 1] or -1 weights : np.ndarray Weights (not necessarily normalized) for each comparison. This can be a list of interval durations Returns ------- score : float Weighted accuracy

### Goal
Computes the duration-weighted accuracy of chord label comparisons to provide a time-proportional evaluation of chord estimation performance.

### Parameters
- `comparisons`: `np.ndarray` containing chord comparison scores, where each element is in the range [0, 1] or is -1.
- `weights`: `np.ndarray` containing weights for each comparison, typically representing the duration of the corresponding chord interval.

### Input
- **Data:** Requires two NumPy arrays of equal length.
- **Preconditions:** The `comparisons` array must contain values in [0, 1] or -1. The `weights` array must contain non-negative values. If using interval-based evaluation, ensure `comparisons` and `weights` are derived from aligned reference and estimated intervals (e.g., via `mir_eval.util.merge_labeled_intervals`).

### Output
Returns `float`: The weighted accuracy score, calculated as the sum of (comparison * weight) divided by the sum of weights.

### Valid Call Patterns
```python
import mir_eval
import numpy as np

# Example using interval durations as weights
comparisons = np.array([1.0, 0.0, 1.0])
durations = np.array([2.5, 1.0, 0.5])
score = mir_eval.chord.weighted_accuracy(comparisons, durations)
```

### LLM Instruction Prompt
- Always ensure `comparisons` and `weights` have the same length before calling `weighted_accuracy`.
- Ensure `weights` are non-negative; the function will raise a `ValueError` if negative weights are provided.
- If all weights are zero, the function will issue a `UserWarning` and return 0.

### Prompt Snippet
```text
mir_eval.chord.weighted_accuracy(comparisons, weights)
```

### Common Failure Modes
- **Length Mismatch:** Passing `comparisons` and `weights` arrays of different lengths will raise a `ValueError`.
- **Negative Weights:** Providing negative values in the `weights` array will raise a `ValueError`.
- **Zero Weights:** If the sum of `weights` is zero, the function warns and returns 0.0.

### Fix Code Hint
```python
# Ensure arrays are aligned and non-negative
if len(comparisons) != len(weights):
    raise ValueError("Comparisons and weights must have the same length.")
if np.any(weights < 0):
    raise ValueError("Weights must be non-negative.")
score = mir_eval.chord.weighted_accuracy(comparisons, weights)
```

## API Test: `weighted_score`

### Signature
```python
def weighted_score(reference_key, estimated_key, allow_descending_fifths=False)
```
_Source: source/mir_eval/key.py:117_

_Source doc:_ Compute a heuristic score which is weighted according to the relationship of the reference and estimated key, as follows: +------------------------------------------------------+-------+ | Relationship                                         | Score | +------------------------------------------------------+-------+ | Same key and mode                                    | 1.0   | +------------------------------------------------------+-------+ | Estimated key is a perfect fifth above reference key | 0.5   | +------------------------------------------------------+-------+ | Relative major/minor (same key signature)            | 0.3   | +------------------------------------------------------+-------+ | Parallel major/minor (same key)                      | 0.2   | +------------------------------------------------------+-------+ | Other                                                | 0.0   | +------------------------------------------------------+-------+ When specifying allow_descending_fifths=True, the scoring changes so that keys that are a perfect fifth above or below the reference key score 0.5 points. This is consistent with the scoring used for MIREX since 2017. In the future, the default behaviour will change to use the new method by default. Examples -------- >>> ref_key = mir_eval.io.load_key('ref.txt') >>> est_key = mir_eval.io.load_key('est.txt') >>> score = mir_eval.key.weighted_score(ref_key, est_key, ...                                     allow_descending_fifths=True) Parameters ---------- reference_key : str Reference key string. estimated_key : str Estimated key string. allow_descending_fifths : bool Specifies whether to score descending fifth errors or not. Returns ------- score : float Score representing how closely related the keys are.

### Goal
Computes a heuristic similarity score between a reference key and an estimated key based on their harmonic relationship, commonly used for evaluating key estimation algorithms.

### Parameters
- `reference_key`: A string representing the ground-truth key (e.g., "C:maj").
- `estimated_key`: A string representing the algorithm's predicted key.
- `allow_descending_fifths`, default `False`: A boolean flag; if `True`, keys a perfect fifth below the reference are also awarded 0.5 points, aligning with MIREX 2017+ standards.

### Input
The function expects two strings representing musical keys. These are typically obtained via `mir_eval.io.load_key()`. Precondition: The input strings must be valid key representations recognized by the library; invalid strings will trigger a `ValueError`.

### Output
Returns `float`: A score between 0.0 and 1.0 representing the harmonic closeness of the estimated key to the reference key.

### Valid Call Patterns
```python
import mir_eval
# Standard usage
score = mir_eval.key.weighted_score("C:maj", "G:maj", allow_descending_fifths=True)

# Using loaded annotations
ref = mir_eval.io.load_key('ref.txt')
est = mir_eval.io.load_key('est.txt')
score = mir_eval.key.weighted_score(ref, est)
```

### LLM Instruction Prompt
- Always use `mir_eval.key.weighted_score` as the function call.
- Ensure both `reference_key` and `estimated_key` are valid key strings.
- Set `allow_descending_fifths=True` when evaluating against modern MIREX-compliant benchmarks.

### Prompt Snippet
```text
# Example of calling weighted_score
score = mir_eval.key.weighted_score(reference_key, estimated_key, allow_descending_fifths=True)
```

### Common Failure Modes
- Passing invalid key strings (e.g., "H:maj" or "C:foo") will raise a `ValueError`.
- Providing non-string inputs for the key arguments will result in a `ValueError` during internal validation.

### Fix Code Hint
```python
# Ensure keys are validated or loaded via mir_eval.io
try:
    score = mir_eval.key.weighted_score(ref, est)
except ValueError as e:
    print(f"Invalid key format: {e}")
```

