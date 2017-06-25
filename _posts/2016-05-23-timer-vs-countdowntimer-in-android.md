---
layout: post
title: Differentiating Timer class and CountDownTimer class in Android
---

Both of these classes are used to schedule repeating execution of codes. The differences between them are summarized as follows. 

1. CountDownTimer needs a terminating time to be set. In other words, it schedule its terminating time. Timer doesn't need to set a terminating time beforehand. 
An analogy is like this: CountdownTimer is like For(some # of iterations){dosomething;}. Timer is like while(true){dosomething;if(something){break;}}

2. CountDownTimer is a non bloking schedule. It means that while it is counting down, the main thread will not be block. At this point, I'm not sure if the CountDownTimer is be executed in a seperate thread. Timer is non to be executing in a seperate thread. As a result, it will not block the execution of the codes in current thread. However, a termination call for a timer is necessary. Otherwiese it will be executing indefinitely. 

3. CountDownTimer synchronizes with itself. It means that the scheduled tasks are executed in sequence. If the previous task is not finished while next task comes out, it will hold the second one until the first one is finished. On the other hand, Timer doesn't have any synchronization mechanism. It simply executes a segment of codes at a regular rate, regardless of whether they are out of order.


Here I provide implementation for future reuse.

{% highlight java %}

private AllCountDownTimers m_countDownTimers;
    private class AllCountDownTimers{
        TextView count_down_text_view;
        public AllCountDownTimers(){
            count_down_text_view=(TextView)findViewById(R.id.countdown_state);
            count_down_text_view.setVisibility(View.VISIBLE);
        }



        public CountDownTimer cd_prepare=new CountDownTimer(m_mapImageView.PREPARE_TIME, 1000) {
            @Override
            public void onTick(long l) {
                count_down_text_view.setText("Data Collection begins in: " + l/ 1000);
            }

            @Override
            public void onFinish() {
                remoteSensorManager.startMeasurement(current_user_name);
                remoteSensorManager.labelActivity(current_gesture);
                cd_brush.start();
            }
        };

        public CountDownTimer cd_brush=new CountDownTimer(m_mapImageView.BRUSHING_TIME, 1000) {
            @Override
            public void onTick(long l) {
                count_down_text_view.setText("Brushing "+m_mapImageView.buttoms_info.get(current_gesture).name  + l/ 1000);
            }

            @Override
            public void onFinish() {

                count_down_text_view.setText("Brushing Finished");
                remoteSensorManager.stopMeasurement();
            }
        };

        public void stop_timers(){
            if(cd_brush!=null && cd_prepare!=null){

                cd_brush.cancel();
                cd_prepare.cancel();
            }
        }



        public Timer m_repeating_timer;
        public void start_train_timer(){
            if(m_repeating_timer!=null){
                m_repeating_timer.cancel();
                m_repeating_timer=null;
            }
            m_repeating_timer=new Timer();
            m_repeating_timer.scheduleAtFixedRate(new TimerTask() {
                @Override
                public void run() {

                    //Called each time when 1000 milliseconds (1 second) (the period parameter)
                    Intent i = new Intent(DataMapKeys.UPDATE_GESTURE);
                    i.putExtra("gesture_id",train_gesture_id++);
                    if(train_gesture_id>m_mapImageView.buttoms_info.size()){
                        train_gesture_id=0;
                        stop_train_timer();
                    }

                    sendBroadcast(i);
                    Log.d(TAG,"try to send intent");
                }
            }, 0, m_mapImageView.BRUSHING_TIME+m_mapImageView.PREPARE_TIME+3000);
        }

        private void stop_train_timer(){
            if(m_repeating_timer!=null){

                m_repeating_timer.cancel();
                m_repeating_timer=null;
            }
            if(m_countDownTimers!=null){
                m_countDownTimers.stop_timers();
            }

        }

    }
{% endhighlight %}

The receiver for the Timer thread:

{%highlight java%}




public BroadcastReceiver receive_new_gesture= new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                final int gestureID=intent.getExtras().getInt("gesture_id");
                Log.d(TAG,"gesture received by main "+ Integer.toString(gestureID));
                set_new_gesture(gestureID);
                current_gesture=gestureID;

                m_countDownTimers.cd_prepare.start();

                }
        };



{%endhighlight%}





