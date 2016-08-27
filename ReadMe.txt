ChatterBox Sample App
---------------------

ChatterBox is a sample application for establishing video calls with WebRTC.
The application was build to support receiving calls even when the application is closed or suspended. 
To achieve this, the call communication logic runs in a background process named BackgroundHost, while 
the UI is handled by a separate process named ChaterBox.exe
  


This file describes steps for building and running the different projects in ChatterBox.sln.

The ChatterBox.sln solution contains 8 projects. 
There are references between these projects. For correct usage read the following sections.

---Building---
There are 4 projects that could be built and run as top projects, others projects will be built as they ar referred to one of the top project:
- ChatterBox.Server             - for server app
- ChatterBox   					- for win10 app (phone and desktop)


---Running---
1. Start ChatterBox.Server on any machine
2. Start ChatterBox universal windows application (on 2 different devices), this will also launch BackgroundHost process as well.
3. In ChatterBox application, access the settings page, set the server's ip address in the "Server Host" field.
4. Save settings, and Press the connect button.


---Making call---
1. Select the peer you would like to call.
2. Press the audio or video button to initiate audio or video call.
3. Call notifications should be received on the remote peer, even if the client application is not in the foreground.
4. Answer the call by pressing the phone button.


---Application Insights logging---
User can switch On "AppInsight Logging" setting to send some metrics & logs to appInsights resource in Azure.
By default all metrics are sent to the following resource:
https://ms.portal.azure.com/#resource/subscriptions/58581a18-1419-4c9d-9741-3ac843b57644/resourceGroups/Group-4/providers/microsoft.insights/components/WebRTC
You could point to your own AppInsight instance by changing AppInsights configuration or 
just updating <InstrumentationKey> in ChatterBox/ApplicationInsights.config file.
  
The following custom events are logged in AppInsights:
 - CallStarted:              This event is logged when incoming/outgoing calls are started. It contains "Timestamp" and 
                             "Connection Type" custom data fields.
 - CallEnded:                This event is logged when calls are ended. It contain the custom fields "Timestamp" and "Call Duration".
 - Network Average Quality:  This event is logged at the end of each call. It captures "Timestamp", "Minimum Inbound Speed" 
                             and "Maximum Outbound Speed" for each call. These are average speeds read from the network adapter. 
 - Audio Codec:              This event is logged at the beginning of the call.
 - Video Codec:              This event is logged at the beginning of the call.
 - Video Height/Width Downgrade: This event is logged during the call if the current video resolution (height/width) is changed during the call. 
 - Application Suspending/Resuming: This event is logged when the application is suspended or resumed. 
 
The following metrics can be found in the "Metrics Explorer" of the AppInsights resource :
 - Old/New Height/Width- These metrics are from "Video Height/Width Downgrade" event.
 - Minimum Inbound/Maximum Outbound Speed - These metrics are taken from the "Network Average Quality" event 
 - Audio/Video Packet Lost Rate - The metric is logged once per minute during a call. It is equal to <sum of sent audio/video packets>/<sum of lost audio/video packets> 
 - Audio/Video Current Delay Rate - The metric is logged after call end. It is equal to <average of audio/video current delays (received from WebRTC)>.
 
 
---WebRTC Tracing---
For collecting RTC Traces, follow those steps:
1. Run a tcp server on remote machine and listen on port 55000. It can be for example Hercules tcp server for windows or SocketTest tcp server for mac.
2. Get the ip address of the machine running the tcp server. (i.e. the PC's ip address). This address should be entered into the ChatterBox application settings (Step 4)
3. In ChatterBox Settings UI, set "RTC Trace server Ip" with ip of the tcp server.
4. Set "RTC Trace server Port" to 55000.
5. Switch On "RTC Trace". Use the toggle button on settings page to enable tracing.


---ETW Statistics---
A Visual Studio (2013 and 2015) extension has been developed to see WebRTC live statistics and events in Visual Studio diagnostic tools.

To be able to see the statistics and events in the diagnostic tool for the ChatterBox application, you will need to turn on 
ETW Statistics before establishing a call. You could toggle this option on in the settings page of the ChatterBox application.
 