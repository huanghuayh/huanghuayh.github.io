---
layout: post
title: Best Ways for Interserivce Communications in Android
---

Previously, I conduct this task using intents


Code for sender side:

{% highlight java %}

Intent i = new Intent(DataMapKeys.FILE_RECEIVE_UPDATE);
i.putExtra("receiving_state",Integer.toString(recieved_count)+"/"+Integer.toString(file_count)+" files are received");
sendBroadcast(i);

{% endhighlight %}

Code for receiver side:

{% highlight java %}
public BroadcastReceiver file_receive_update= new BroadcastReceiver() {
     @Override
     public void onReceive(Context context, Intent intent) {
         TextView tv=(TextView)findViewById(R.id.transmission_state);
         tv.setVisibility(View.VISIBLE);
         tv.setText(intent.getExtras().getString("receiving_state"));
     }
 };
registerReceiver(file_receive_update, new IntentFilter(DataMapKeys.FILE_RECEIVE_UPDATE));
unregisterReceiver(file_receive_update);         

{% endhighlight %}

Another way to achieve the same goal is as follows:

Sender side:

{% highlight java %}
BusProvider.postOnMainThread(new SensorUpdatedEvent(sensor, dataPoint));


public final class BusProvider
{
    private static final Bus BUS = new Bus();

    public static Bus getInstance() {
        return BUS;
    }

    public static void postOnMainThread(final Object event) {
        Handler handler = new Handler(Looper.getMainLooper());

        handler.post(new Runnable() {
            public void run() {
                BUS.post(event);
            }
        });
    }

    private BusProvider() {
    }
}

{% endhighlight %}


Event class definition:

{% highlight java %}

public class SensorUpdatedEvent {
    private Sensor sensor;
    private SensorDataPoint sensorDataPoint;

    public SensorUpdatedEvent(Sensor sensor, SensorDataPoint sensorDataPoint) {
        this.sensor = sensor;
        this.sensorDataPoint = sensorDataPoint;
    }

    public Sensor getSensor() {
        return sensor;
    }

    public SensorDataPoint getDataPoint() {
        return sensorDataPoint;
    }
}




{% endhighlight %}


Receiver side:

{% highlight java %}
@Subscribe
public void onSensorUpdatedEvent(final SensorUpdatedEvent event) {
    // do something
}


{% endhighlight %}
