{% highlight python %}
import tensorflow as tf
import random

def get_experiment():
    """ Flips a virtual coin n times, and tracks frequency of heads (1) occurring

    Constructs graph.
    Returns [merged, frequency, trials]
      - merged: a summary op that must be run to generate summaries
      - frequency: the experiment op
      - trials: the placeholder for feed_dict to work
    """
    trials = tf.placeholder(tf.int32) # Trials

    # Count number of 1s (heads) in sample.
    elements_equal_to_value = tf.equal(trials, 1) # Eg [true, false, true, true]
    as_ints = tf.cast(elements_equal_to_value, tf.int32) # Eg. [1, 0, 1, 1]
    count = tf.reduce_sum(as_ints) # Eg. 3

    # Calculate relative frequency
    frequency = tf.divide(count, tf.shape(trials)[0])
    tf.summary.scalar('frequency', frequency) # relative frequency of occurrence of heads

    # Calculate abs of deviation from expectation 0.5.
    deviate = tf.abs(tf.subtract(0.5, tf.cast(frequency, tf.float32)))
    tf.summary.scalar('deviate', deviate)

    merged = tf.summary.merge_all() # Collect all summary ops into 'merged'

    # deviate is also run because 'merged' is run. See run()

    return merged, frequency, trials

def get_trials(n, isTF=True):
    """Gets trials using random function from TensorFlow or Python.
    Args: isTF --- if True, use TensorFlow's random function; Python's otherwise
    """
    if isTF:
        t = tf.random_uniform([n], 0, 2, dtype=tf.int32) # Trials
    else:
        array = []
        for i in range(n):
            array.append(random.randrange(0, 2))
        t = tf.constant(array)
    # Equivalent to sess.run(t)
    t = t.eval() # Uses sess. Possible only for InteractiveSession
    return t

def run():
    sess = tf.InteractiveSession() # InteractiveSession allows get_trials() to use sess.

    # Notice that feed_dict (in sess.run()) uses an actual tf.placeholder object!
    merged, frequency, trials = get_experiment()

    tfr_writer = tf.summary.FileWriter('log/tf', sess.graph)
    pyr_writer = tf.summary.FileWriter('log/py')

    for i in range(1, 300 + 1):
        summary, _ = sess.run([merged, frequency], feed_dict={trials: get_trials(i)})
        tfr_writer.add_summary(summary, i)
        if i % 10 == 0:
            print("Executing n = " + str(i))
            summary, _ = sess.run([merged, frequency], feed_dict={trials: get_trials(i, False)})
            pyr_writer.add_summary(summary, i)

    sess.close()

def main():
    run()

if __name__ == "__main__":
    main()
{% endhighlight %}
