Android
=======

Adb and system notes
--------------------

### Universal drivers

<https://github.com/koush/UniversalAdbDriver>

### Mount /system read-only

```bash
adb shell
su
mount -o remount,rw /system
```

### Superuser

Superuser by clockworkmod is the most reliable x86 su program (in my experience).

Android application conventions
-------------------------------

### Application lifespan and threading/processing

Android has a main UI thread. Events are posted to this asynchronously and handled in some order accordingly. Thus, doing something in the UI thread which is very long-running will lock up other UI events. You can't do UI manipulations outside the UI thread (but you _can_ use a Handler with message passing and a normal Java subthread to perform UI updates from a separate thread). You can specifically use activity.runOnUiThread if you have access to the activity object.

_Asynchronous callbacks are misleading_. Even if a callback seems like it would be asynchronous, callbacks are posted to the main thread. Thus, if you have a synchronization dependency between the main thread and the callback (e.g., main thread code depends on the callback completion), you will deadlock!

Normal threads and executors will pause if the screen turns off (machine goes into deep sleep, thread will randomly start/stop executing as the CPU is woken up by the OS). Need to use an alarm manager if you need it to be reliably running in the background.

Services perform background tasks without providing an UI. They can notify the user via the notification framework in Android. HOWEVER, even background services run on the UI thread unless otherwise mentioned! Don't put long-running operations in a UI thread! To make the service not close itself except in rare circumstances, and start itself back up if it does end up getting stopped, start the service via `startService()` (rather than binding) and then in the `onStartCommand()`, return `START_STICKY`.

BroadcastReceiver can be registered to receives system messages and Intents. BroadcastReceiver will get notified by the Android system if the specified situation happens. For example a BroadcastReceiver could get called once the system completed its boot process or if a phone call is received.

Don't overuse Activities and Services. The interthread communication is not at all straightforward. Try to only use services for communication between apps, not within one.

### Visual components

Activity represents the presentation layer of an Android application. A simplified (and slightly incorrect) description is that an Activity is a a screen which the user sees. This is slightly incorrect as Activities can be displayed as dialogs or transparent. An Android application can have several activities and it can be switched between them during runtime of the application.

Views are user interface widgets, e.g. buttons or text fields. The base class for all Views is android.view.View. The layout of the Views is managed by subclasses of type android.view.ViewGroups. Views often have attributes which can be used to change their appearance and behavior.


### Intents

Intents are asynchronous messages which allow the application to request functionality from other components of the Android systen, e.g. from Services or Activities. An application can call a component directly (explicit intent) or ask the Android system to evaluate registered components for a certain Intents (implicit intents). For example the application could implement sharing of data via an Intent and all components which allow sharing of data would be available for the user to select. Applications register themselves to an intent via an IntentFilter. Intents allow to combine loosely coupled components to perform certain tasks. Typically in android, `_ACTION` after a string means it's an intent.

An Intent is exactly what it describes. It's an "intention" to do an action. An Intent is basically a message to say you did or want something to happen. Depending on the intent, apps or the OS might be listening for it and will react accordingly. Think of it as a blast email to a bunch of friends, in which you tell your friend John to do something. The other folks will ignore the email, but John will react to it.  To listen for an intent (like the phone ringing, or an SMS is received), you implement a broadcast receiver. If you want to fire off an intent to do something, like pop up the dialer, you fire off an intent saying you will.  To send an intent from adb: `adb shell am broadcast -a com.whereismywifeserver.intent.TEST --es sms_body "test from adb"`.

Broadcasts WILL NOT BE RECEIVED if an application hasn't ever been launched since it was installed; run a dummy activity if necessary. An application is in the "stoped state" until an activity on it has been launched (some cockeyed security measure).


### Context

As the name suggests, context is the context of current state of the application/object. It lets newly created objects understand what has been going on. Typically you call it to get information regarding another part of your program (activity, package/application). You can get the context by invoking `getApplicationContext()`, `getContext()`, `getBaseContext()` or `this` (when in the activity class). Activities are a subclass of context.


Image resource folders
----------------------

These automatically scale images depending on the DPI. To disable this autoscaling, place the image in the drawable-nodpi folder.


Snippets
--------

### Threads and Runnables (Java convention)

Used to execute a chunk of code in its own thread. Better to implement a runnable than extend a thread (more flexible to implement than extend; can reuse the runnable later if necessary).

```java
public class ThreadA implements Runnable {
    public void run() {
        //Code
    }
}
//with a "new Thread(threadA).start()" call

public class ThreadB extends Thread {
    public ThreadB() {
        super("ThreadB");
    }
    public void run() {
        //Code
    }
}
//with a "threadB.start()" call
```

### Timers and ExecutorServices (Java convention)

Allows for the scheduling of repeating tasks. ExecutorServices are newer and all-around better (can also handle multi-threaded pools). Always prefer (>Java 5).

```java
ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor();
Runnable task = new Runnable(){
  @Override
  public void run() {
      System.out.println("printing");
  }
};
executor.scheduleAtFixedRate(task, 0, 3, TimeUnit.SECONDS);
```

### AlarmManager (Android convention)

Allows for scheduled tasks, but is more heavy-weight. Is managed by the OS itself, so the task will launch even if the CPU is in deep sleep (e.g., screen off; _none of these other scheduling methods accomplish this unless you grab a wake lock!_)

```java
public class LoggerAlarm extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        try {
            Logger.logData(context);
        } catch (SettingNotFoundException | IOException e) {
            e.printStackTrace();
        }
    }

    public void SetAlarm(Context context) {
        AlarmManager am = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
        Intent i = new Intent(context, LoggerAlarm.class);
        PendingIntent pi = PendingIntent.getBroadcast(context, 0, i, 0);
        am.setRepeating(AlarmManager.RTC_WAKEUP, System.currentTimeMillis(), 1000 * 30, pi); // Millisec * Second
    }

    public void CancelAlarm(Context context) {
        Intent intent = new Intent(context, LoggerAlarm.class);
        PendingIntent sender = PendingIntent.getBroadcast(context, 0, intent, 0);
        AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
        alarmManager.cancel(sender);
    }
}
```

### AsyncTask (Android convention)

Enables proper and easy use of the UI thread. This class allows to perform background operations and publish results on the UI thread without having to manipulate threads and/or handlers.

```java
class TestAsynch extends AsyncTask<Void, Integer, String>
{
    protected void onPreExecute (){
        Log.d("PreExceute","On pre Exceute......");
    }

    protected String doInBackground(Void...arg0) {
        Log.d("DoINBackGround","On doInBackground...");

        for(int i=0; i<10; i++){
            Integer in = new Integer(i);
            publishProgress(i);
        }
        return "You are at PostExecute";
    }

    protected void onProgressUpdate(Integer...a){
        Log.d("You are in progress update ... " + a[0]);
    }

    protected void onPostExecute(String result) {
        Log.d(""+result);
    }
}
new TestAsynch().execute();
```

### Handler (Android convention)

Allows you to send and process ("post") Message and Runnable objects to a thread's MessageQueue (frequently the UI thread). Typically prefer AsyncTasks.

### Send data to a server via POST

```java
HttpClient httpclient = new DefaultHttpClient();
HttpPost httppost = new HttpPost("http://shook.id/logger.php");

List`<NameValuePair>` nameValuePairs = new ArrayList<>();
nameValuePairs.add(new BasicNameValuePair("data", dataString));

httppost.setEntity(new UrlEncodedFormEntity(nameValuePairs));
httpclient.execute(httppost);
```

