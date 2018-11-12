#### Tensorflow Example
<a href="https://github.com/ModelChimp/tensorflow_example">https://github.com/ModelChimp/tensorflow_example</a>

    from __future__ import absolute_import
    from __future__ import division
    from __future__ import print_function

    import argparse
    import sys
    import math
    import timeit

    from tensorflow.examples.tutorials.mnist import input_data
    import tensorflow as tf

    FLAGS = None

    # MODELCHIMP tracker
    from modelchimp import Tracker

    def deepnn(x):
      x_image = tf.reshape(x, [-1, 28, 28, 1])
      W_conv1 = weight_variable([5, 5, 1, 32])
      b_conv1 = bias_variable([32])
      h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
      h_pool1 = max_pool_2x2(h_conv1)
      W_conv2 = weight_variable([5, 5, 32, 64])
      b_conv2 = bias_variable([64])
      h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
      h_pool2 = max_pool_2x2(h_conv2)
      W_fc1 = weight_variable([7 * 7 * 64, 1024])
      b_fc1 = bias_variable([1024])
      h_pool2_flat = tf.reshape(h_pool2, [-1, 7*7*64])
      h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)
      keep_prob = tf.placeholder(tf.float32)
      h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob)
      W_fc2 = weight_variable([1024, 10])
      b_fc2 = bias_variable([10])
      y_conv = tf.matmul(h_fc1_drop, W_fc2) + b_fc2

      return y_conv, keep_prob


    def conv2d(x, W):
      return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')


    def max_pool_2x2(x):
      return tf.nn.max_pool(x, ksize=[1, 2, 2, 1],
                            strides=[1, 2, 2, 1], padding='SAME')


    def weight_variable(shape):
      initial = tf.truncated_normal(shape, stddev=0.1)
      return tf.Variable(initial)


    def bias_variable(shape):
      initial = tf.constant(0.1, shape=shape)
      return tf.Variable(initial)


    def main(_):
      # Import data
      mnist = input_data.read_data_sets(FLAGS.data_dir, one_hot=True)
      param = {}

      param['data_length'] = mnist.train._num_examples
      param['batch_size'] = 200
      param['epoch_num'] = 10
      param['steps_num'] = math.floor(param['data_length'] / param['batch_size']) - 1
      param['optimzer'] = 'Adam'
      param['cost_function'] = 'cross entropy'
      param['learning_rate'] = 0.01

      # Create the model
      x = tf.placeholder(tf.float32, [None, 784])

      # Define loss and optimizer
      y_ = tf.placeholder(tf.float32, [None, 10])

      # Build the graph for the deep net
      y_conv, keep_prob = deepnn(x)

      cross_entropy = tf.reduce_mean(
          tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=y_conv))
      train_step = tf.train.AdamOptimizer(param['learning_rate']).minimize(cross_entropy)
      correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_, 1))
      accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

      tracker = Tracker('<PROJECT KEY>', host='localhost:8000', experiment_name='MNIST Classification') #MODELCHIMP
      tracker.add_multiple_params(param) #MODELCHIMP

      with tf.Session() as sess:
          sess.run(tf.global_variables_initializer())

          for i in range(param['epoch_num']):
            # snapshot start time
            start_time = timeit.default_timer()

            for j in range(param['steps_num']):
              batch = mnist.train.next_batch(50)
              train_step.run(feed_dict={x: batch[0], y_: batch[1], keep_prob: 0.5})

            # snapshot start time
            end_time = timeit.default_timer()

            train_accuracy = accuracy.eval(feed_dict={
                x: batch[0], y_: batch[1], keep_prob: 1.0})

            test_accuracy = accuracy.eval(feed_dict={
                x: mnist.test.images, y_: mnist.test.labels, keep_prob: 1.0})
            tracker.add_metric("accuracy_train", float(train_accuracy), i) #MODELCHIMP
            tracker.add_metric("accuracy_test", float(test_accuracy), i) #MODELCHIMP
            tracker.add_duration_at_epoch("Train", end_time - start_time, i) #MODELCHIMP

            print("Epoch %s: train_accuracy = %s, test_accuracy = %s" % (i, train_accuracy, test_accuracy))

    if __name__ == '__main__':
      parser = argparse.ArgumentParser()
      parser.add_argument('--data_dir', type=str,
                          default='/tmp/tensorflow/mnist/input_data',
                          help='Directory for storing input data')
      FLAGS, unparsed = parser.parse_known_args()
      tf.app.run(main=main, argv=[sys.argv[0]] + unparsed)
