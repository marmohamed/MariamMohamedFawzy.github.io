---
layout: post
title:  "Transfer weights from a pretrained model to specific layers in Tensorflow"
date:   2020-5-26
categories: 
tags: tensorflow
---
In this post, I have a big models with many modules that I want to train separately. <br>
Also, I have a module that I change alot, but training the whole model from scratch will be time-consuming, so a solution is to train and freeze the modules that will not change, then load them when resume training the chaged module. 

In tensorflow, each checkpoint has three separate files.

1. A .meta file \\
It contains the graph (architecture) of the model. It knows the tensors but does not know their values.
<br><br>
2. A .index file \\
An index or a dictionary. Each key is a tensor's name and its value is where to find the value of that tensor in the .data file. 
<br><br>
3. A .data file \\
It contains the values of the variables/tensors.
<br><br>

In the following steps, we will load the variables from an old graph/model to a new one.

<br>

1. We have two graphs, an old one and a new one.

``` python
# old graph
graph1 = tf.Graph()
# new graph
graph2 = tf.Graph()
```

2. First, we load the old model and load of its variables.

``` python
## global_step can be a value or nothing, so replace it with your value

# Load the meta graph
with graph1.as_default():
    saver = tf.train.import_meta_graph('model.ckpt-global_step.meta')

# get the variables from graph 1
variables = graph1.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)

```

3. After that, we get the variables of the new model and store them in a dictionary accessed by their names.

``` python
# get the variables from graph 2 and store them in a dictionary with key = name and value = the variable itself to be able to access variables by name
new_variables = graph2.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)
new_variables_dict = {variable.name: variable for variable in new_variables}

```

4. Then we run a tensorflow session to get the values of the tensors from the old graph.

``` python
## global_step can be a value or nothing, so replace it with your value
# get the values of the variables of graph 1
with graph1.as_default():
    with tf.Session() as sess:  
        # saver.restore(sess, tf.train.latest_checkpoint('./'))
        # or
        saver.restore(sess, "model.ckpt-global_step")
        var_values = sess.run(variables)
```

5. Since the two graphs may be different, we keep the mutual tensors for the next step.

``` python
# filter the variables that exist in both graphs
assigns = dict()
for i in range(len(variables)):
    if variables[i].name in new_variables_dict:
        assigns[variables[i].name] = var_values[i]

```

6. 

``` python
with graph2.as_default():
    assigns2 = []
    # for every variable, we will make a tf.assign operation
    for k, v in assigns.items():
        try:
            assigns2.append(tf.assign(new_variables_dict[k], v))
        except:
            pass
    with tf.Session() as sess:
        # This init is to initialize all variables first before modifying the pretrained variables as there may be some variables in the new graph that are not in the old graph.
        sess.run(tf.global_variables_initializer())
        sess.run(assigns2)

```


Here is the complete code: <br>

``` python

graph1 = tf.Graph()
graph2 = tf.Graph()

# Load the meta graph
with graph1.as_default():
    saver = tf.train.import_meta_graph('model.ckpt.meta')

variables = graph1.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)

new_variables = graph2.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)
new_variables_dict = {variable.name: variable for variable in new_variables}


with graph1.as_default():
    with tf.Session() as sess:  
        # saver.restore(sess, tf.train.latest_checkpoint('./'))
        # or
        saver.restore(sess, "model.ckpt")
        var_values = sess.run(variables)

assigns = dict()
for i in range(len(variables)):
    if variables[i].name in new_variables_dict:
        assigns[variables[i].name] = var_values[i]


with graph2.as_default():
    assigns2 = []
    for k, v in assigns.items():
        try:
            assigns2.append(tf.assign(new_variables_dict[k], v))
        except:
            pass
    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        sess.run(assigns2)

```