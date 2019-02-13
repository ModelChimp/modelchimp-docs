#Tracker Class
Tracker class is instantiated with a project key and the domain name of Marble's ModelChimp server.

    from modelchimp import Tracker

    # Add before the trained model
    tracker = Tracker(key='<PROJECT KEY>',
                      host='<host>',
                      experiment_name=None,
                      tracking=True,  
                      auto_log=False,
                      existing_exp_id=None)


## Attribute
  * `key`: Project key which is given in the ModelChimp project page
  * `host`: Domain name of the ModelChimp server
  * `experiment_name(optional)`: Give a custom name to the experiment. If not given then the experiment id is assigned
  * `tracking`: if set to False then experiment won't be tracked
  * `auto_log`: if set to True then ModelChimp automatically picks up the parameters of the model for which fit is called first. Currently, it works for sklearn only
  * `existing_exp_id`: Logs the details to an existing experiment for which the id is given


## Methods


### add_custom_object()
Upload a python object to ModelChimp

    add_custom_object(name, object)

<b>Arguments</b>

* **name:** String Name of the object
* **object:** Object Python object to be stored

### add_dataset_id()
Add a dataset id for the experiment

    add_dataset_id(id)

<b>Arguments</b>

* **id:** String|Int Id of the dataset

### add_duration_at_epoch()
Log the duration at a particular epoch

    add_duration_at_epoch(tag, seconds_elapsed, epoch)

<b>Arguments</b>

* **tag:** Name of the duration
* **seconds_elapsed:** Number of seconds elapsed for the duration
* **epoch:** Current epoch number


### add_image()
Upload image. This is useful for computer vision use cases

    add_image(filepath, metric_dict=None, custom_file_name=None, epoch=None)

<b>Arguments</b>

* **filepath:** File path of the image
* **metric_dict:** Dict of metrics to be stored for the image
* **custom_file_name:** An alternate name to be used for storing the image
* **epoch:** Epoch at which the image was used for prediction

### add_metric()
Add the metrics that need to be tracked

    add_metric(metric_name, metric_value, epoch=None)

<b>Arguments</b>

* **metric_name:** String Defines name of the metric
* **metric_value:** Int|Float Represents the value of the metric
* **epoch:** Int If None then only 1 instance of the metric will be tracked

### add_model_params()
Extract the parameters out of a model object. Currently, only for sklearn objects.

    add_model_params(model, model_name=None)

<b>Arguments</b>

* **model:** Model object whose parameters needs to be extracted
* **model_name(optional):** Name of model for which the model parameters wil be stored. The name will be prefixed to each of the model parameter.


### add_multiple_metrics()
Add a dictionary of metrics

    add_multiple_metrics(metrics_dict, epoch=None)

<b>Arguments</b>

* **metrics_dict:** Dict Dictionary contains metrics to be tracked
* **epoch:** Int  If None then only 1 instance of each metric will be tracked


### add_multiple_params()
Add a dictionary of parameters

    add_multiple_params(params_dict)

<b>Arguments</b>

* **params_dict:** Dict Dictionary containing parameters to be tracked


### add_param()
Add the parameters that need to be tracked

    add_param(param_name, param_value)

<b>Arguments</b>

* **param_name:** String which defines name of the parameter
* **param_value:** Int|Float|Bool|String that represents the value of the parameter


### pull_custom_object()
Pull python object stored in ModelChimp server

    pull_custom_object(id)

<b>Arguments</b>

* **id:** String Id of the object given in the ModelChimp web portal



### pull_params()
Pull parameters of an experiment locally

    pull_params(experiment_id)

<b>Arguments</b>

* **experiment_id:** String Experiment id of the experiment. For example: experiment_id='11f46fec228120cce93cea0bfa0c06b2a8e9a7ebc6e91f1ddb2c145901ea8259'
