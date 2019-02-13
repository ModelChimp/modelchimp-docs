# Keras Callback

ModelChimp provides a Keras Callback class that can be used for logging the training metrics.

```Python
from modelchimp.keras import ModelChimpCallback

.
.
.

model.fit(x_train, y_train,
          batch_size=param['batch_size'],
          epochs=param['batch_size'],
          verbose=1,
          validation_data=(x_test, y_test),
          callbacks=[ModelChimpCallback(),]) # MODELCHIMP Callback

```
