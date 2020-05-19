---
layout: post
title:  "Flooding Loss"
date:   2020-5-5
categories: 
tags: papers_analysis
---

<!-- <link href="https://fonts.googleapis.com/css?family=Prompt&display=swap" rel="stylesheet"> -->
<!-- <link rel="stylesheet" href="{{'/assets/css/style_font.css'}}" /> -->
<link rel="stylesheet" href="{{'/assets/css/style_categories.css'}}" />

{% if post %}
    {% assign tags = post.tags %}
{% else %}
    {% assign tags = page.tags %}
{% endif %}

{% if tags.size > 0 %}
<div class="post-tags catlinks">
  
  <a>Categories</a> :&nbsp;
  {% for tag in tags %}
  <a href="{{site.baseurl}}/tag/{{tag|slugize}}">{{tag}}</a>
  {% unless forloop.last %} &nbsp;| &nbsp;{% endunless %}
  {% endfor %}
</div>
{% endif %}

<h2>This post is incomplete yet</h2>
<hr>

<h3> No Flood </h3>

<img src="/assets/images/no_flood_1.png" alt="No flood loss" style="width: 600px; height=600px;"/>

<h3> Flood loss with fixed flooding value b </h3>

<img src="/assets/images/flood_fixed_b.png" alt="Flood Loss with fixed b" style="width: 600px; height=600px;"/>

<h3> Flood loss with varied flooding value b </h3>

Training the modal depends on choosing a sensitive value of the hyper parameter b, which requires more effort and time to choose. 
<br>
Even by grid search, we may end up with a  value that is not the optimal one. But making the model decides which value is the best for training will make things easier and make this technique more usable.
<br>


This flood loss is based on preventing the model from reaching the zeros loss thus prevents overfitting, in which the validation loss goes up while the training loss goes down. Therefore, we can depend on the validation loss to decide on the value for the flooding parameter b.
<br>
I assume that the overfitting starts when the validation loss increases and the training loss decreases, so we start the flooding from a very small value and increase it as the validation loss increases, and decrease it when the validation loss decreases. 
<br>
To make it abit reasonable, I observe the last n validation losses and calculate the difference between that last average and the current one after adding the last loss, then decide to increase or decrease the flooding value by a certain multiplier.
<br>

``` python

def loss_wrapper(b = b):
    def Loss_compute(y_true, y_pred, b = b):
        loss = keras.losses.categorical_crossentropy(y_true, y_pred)
        loss = K.abs(loss - b) + b
        return loss
    return Loss_compute

b = 0.01
last_loss = []
loss_queue_len = 5
mult = 4.
decrease_mult = 0.99

history_fl = {'loss': [], 'val_loss': [], 'accuracy': [], 'val_accuracy': []}
bs = []
mults = []
last_average = 0.
found = False
first = True
small_loss = float('inf')
for i in range(epochs):
  print('Start epoch', i+1)

  if len(last_loss) >= loss_queue_len:
        current_average = sum(last_loss) / loss_queue_len
        if current_average > last_average:
            b *= mult
            # last_loss = []
            last_loss.pop(0)
            last_loss.pop(1)
        else:
            if not first:
              b /= mult
              # last_loss = []
              last_loss.pop(0)
              last_loss.pop(1)
            else:
              first = False

        last_average = current_average

  mults.append(mult)
  if mult * decrease_mult > 1:
    mult *= decrease_mult
  
  model_fl.compile(loss=loss_wrapper(b = b),
              optimizer=opt,
              metrics=['accuracy'])     
  history_fl_epoch = model_fl.fit(x_train, y_train,
            batch_size=batch_size,
            epochs=1,
            validation_data=(x_valid, y_valid),
            shuffle=True)
  
  for k in list(history_fl_epoch.history.keys()):
    history_fl[k].extend(history_fl_epoch.history[k])

  last_loss.append(history_fl_epoch.history['val_loss'][0])
  if history_fl_epoch.history['val_loss'][0] < small_loss:
    small_loss = history_fl_epoch.history['val_loss'][0]
    model_fl.save('model_fl.h5')

  if len(last_loss) == loss_queue_len and not found:
    last_average = sum(last_loss) / loss_queue_len
    found = True

  if len(last_loss) > loss_queue_len:
    last_loss.pop(0)

  bs.append(b)

```
<img src="/assets/images/flood_varied_b.png" alt="Flood Loss with varied b" style="width: 600px; height=600px;"/>


<img src="/assets/images/bs.png" alt="B values" style="width: 600px; height=600px;"/>


<img src="/assets/images/mults.png" alt="Multiplier values" style="width: 600px; height=600px;"/>


Accuracy and Loss of the final model after 100 epochs(no early stopping):
<br>

<table>
<th>

<td> Accuracy
</td>
<td> Loss
</td>
</th>

<tr>
<td> No flooding
</td>
<td> 0.692
</td>
<td> 2.703
</td>
</tr>

<tr>
<td> Flooding with fixed b
</td>
<td> 0.676
</td>
<td> 1.024
</td>
</tr>

<tr>
<td> Flooding with varied b
</td>
<td> 0.709
</td>
<td> 1.987
</td>

</tr>
</table>

Here is the [keras implementation](https://colab.research.google.com/drive/1oltycQ_kC4wpf0AywFwZLRAg5stvzWBG).

