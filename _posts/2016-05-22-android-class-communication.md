---
layout: post
title: Use Intent to communicate between classes in Android
---
The best way is to use the Intent class. I find the following example useful. 

in the reciving class, add the following broadcast receiver member.
{% highlight java %}
private BroadcastReceiver receive_new_gesture= new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            int gestureID=intent.getExtras().getInt("gesture_id");
            Log.d(TAG,"gesture received by main "+ Integer.toString(gestureID));
            set_new_gesture(gestureID);

        }
    };
{% endhighlight %}

Then register this receiver in onCreate()

{% highlight java %}
registerReceiver(receive_new_gesture, new IntentFilter(DataMapKeys.UPDATE_GESTURE));
{% endhighlight  %}



To send such a message, the sender class do the following:

{% highlight java %}
Intent i = new Intent(DataMapKeys.UPDATE_GESTURE);
i.putExtra("gesture_id",gestureID);
sendBroadcast(i);
{% endhighlight  %}



That's it!
