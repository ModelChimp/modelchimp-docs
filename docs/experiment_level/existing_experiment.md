# Existing Experiment

The information of an experiment can be uploaded to an existing previous experiment by setting the existing_exp_id parameter in the Tracker class

```python
# Add before the trained model
tracker = Tracker(key='<PROJECT KEY>',
                  host='<host>',
            existing_exp_id='94c8cf52b5f34c4b03f5479300921220fb6e4596fa4093d38ab2c3e1907c3b0e')

```
