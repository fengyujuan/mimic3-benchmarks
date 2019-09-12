# More on validating results

The `validate_events.py` tries to assert some assumptions about the data; find events with specific problems and fix these problems if possible.  
Assumptions we assert:
* There is one-to-one mapping between HADM_ID and ICUSTAY_ID in `stays.csv` files.
* HADM_ID and ICUSTAY_ID are not empty in `stays.csv` files.
* `stays.csv` file is always present.
* `inputevents_mv.csv` is not always present. 
* There is no case, where after initial filtering we cannot recover empty ICUSTAY_IDs.
  
For existing inputevents_mv.csv, problems we fix (the order of this steps is fixed):
* Remove all events for which HADM_ID is missing.
* Remove all events for which HADM_ID is not present in `stays.csv`.
* If ICUSTAY_ID is missing in an event and HADM_ID is not missing, then we look at `stays.csv` and try to recover ICUSTAY_ID.
* Remove all events for which we cannot recover ICUSTAY_ID.
* Remove all events for which ICUSTAY_ID is not present in `stays.csv`.

Here is the output of `validate_events.py`:

| Type | Description | Number of rows |
| --- | --- | --- |
| `n_events` | total number of events | 2909053 |
| `empty_hadm` | HADM_ID is empty in `inputevents_mv.csv`. We exclude such events. | 0 |
| `no_hadm_in_stay` | HADM_ID does not appear in `stays.csv`. We exclude such events. | 360715 |
| `no_icustay` | ICUSTAY_ID is empty in `events.csv`. We try to fix such events. | 1007 |
| `recovered` | empty ICUSTAY_IDs are recovered according to stays.csv files (given HADM_ID) | 1007 |
| `could_not_recover` | empty ICUSTAY_IDs that are not recovered. This should be zero. | 0 |
| `icustay_missing_in_stays` | ICUSTAY_ID does not appear in stays.csv. We exclude such events. | * |
| `no_inputevents_mv_subjects`| SUBJECT_ID does not have inputevents_mv.csv. We don't validate these subjects. |*|

