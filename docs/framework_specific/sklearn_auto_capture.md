# Sklearn Auto Capture

ModelChimp can capture the parameters of the Sklearn models automatically without manually specifying them by setting the auto_log parameter in the Tracker class to True

```python
# Add before the trained model
tracker = Tracker(key='<PROJECT KEY>',
                  host='<host>',
                  auto_log=True,)

```
