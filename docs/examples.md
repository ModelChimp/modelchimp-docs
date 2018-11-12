# Sklearn Example

    from patsy import dmatrices
    from sklearn.model_selection import train_test_split
    import sklearn.ensemble as ske
    from sklearn.metrics import accuracy_score, precision_score
    import numpy as np
    import pandas as pd

    # IMPORT MODELCHIMP
    from modelchimp import Track

    #Read the data
    df = pd.read_csv("data/train.csv")
    test_data = pd.read_csv("data/test.csv")


    # Create an acceptable formula for our machine learning algorithms
    formula_ml = 'Survived ~ C(Pclass) + C(Sex) + Age + SibSp + Parch + C(Embarked)'

    # Remove NaN values
    df = df.dropna()

    # Get the required columns
    y, x = dmatrices(formula_ml, data=df, return_type='dataframe')

    # Split the data into train and test
    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.33, random_state=42)

    # Convert the dependent variable to a 1D numpy array
    y_train = np.asarray(y_train).ravel()
    y_test = np.asarray(y_test).ravel()

    # Instantiate and fit our model
    random_forest_model = ske.RandomForestClassifier(n_estimators=100).fit(x_train, y_train)

    # Prediction on test
    y_pred = random_forest_model.predict(x_test)

    # Get the evaluation metrics
    accuracy = accuracy_score(y_test, y_pred)
    precision = precision_score(y_test, y_pred)

    # ModelChimp tracking code
    tracker = Track(key="<PROJECT KEY>")
    tracker.add_evaluation("accuracy", accuracy)
    tracker.add_evaluation("precision", precision)

    tracker.save()

# Keras Example
    from keras import backend as K
    from keras.models import Sequential
    from keras.datasets import mnist
    from keras.layers import Dense, Activation, Convolution2D, MaxPooling2D, Flatten
    from keras.utils import np_utils
    K.set_image_dim_ordering('th')

    # IMPORT MODELCHIMP
    from modelchimp import Track

    img_rows, img_cols = 28, 28
    nb_classes = 10

    nb_filters = 5 # the number of filters
    nb_pool = 2 # window size of pooling
    nb_conv = 3 # window size of filter

    # load and process the data
    (X_train, y_train), (X_test, y_test) = mnist.load_data()

    X_train = X_train.reshape(X_train.shape[0], 1, img_rows, img_cols)
    X_test = X_test.reshape(X_test.shape[0], 1, img_rows, img_cols)
    X_train = X_train.astype('float32')
    X_test = X_test.astype('float32')
    X_train /= 255
    X_test /= 255


    # convert class vectors to binary class matrices
    Y_train = np_utils.to_categorical(y_train, nb_classes)
    Y_test = np_utils.to_categorical(y_test, nb_classes)

    # define the deep learning layers
    nb_epoch = 2
    model = Sequential()
    model.add(Convolution2D(nb_filters, nb_conv, nb_conv, input_shape=(1, img_rows, img_cols)))
    model.add(Activation('relu'))
    model.add(Convolution2D(nb_filters, nb_conv, nb_conv))
    model.add(Activation('relu'))
    model.add(MaxPooling2D(pool_size=(nb_pool, nb_pool)))
    model.add(Flatten())
    model.add(Dense(128))
    model.add(Activation('relu'))
    model.add(Dense(nb_classes))
    model.add(Activation('softmax'))

    # train and evaluate the model
    model.compile(loss='categorical_crossentropy', optimizer="adadelta", metrics=['accuracy','mae'])
    model_history = model.fit(X_train, Y_train, epochs=nb_epoch, batch_size=256, verbose=2,  validation_split=0.2)

    accuracy = model_history.history['acc'][-1]
    mean_absolute_error = model_history.history['val_mean_absolute_error'][-1]

    # ModelChimp tracking code
    tracker = Track(key="<PROJECT KEY>")
    tracker.add_evaluation("accuracy", accuracy)
    tracker.add_evaluation("mean_absolute_error", mean_absolute_error)

    tracker.save()
