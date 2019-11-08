This is a fork of the [iotcomms implementation for general webrtc](https://github.com/iotcomms/iotcomms-react-webrtc)

An example application is [found here](https://github.com/keyroii/freepbx-react-webrtc-example)

To get started:

`$ npm install freepbx-react-webrtc --save`

The main objective of the component is to provide a high-level logic providing WebRTC functionality to web applications where the developer should not have to dig into the details of how SIP, Peer connections, Sessions and Video elements interact during the lifecycle of a call.

It provides high level configuration through React.js Component properties. Once the component is mounted it will establish a connection back to the provisioned SIP server via WebSocket connection. From now it is ready to place and receive calls and it provides user interface to interact with the calls.

## Events that are called by component

| Event        | Description           |
| ------------- |-------------:|
| Idle      | Called when connected to service and there is no call coming in or going out |
| Calling      | Called when there is an outgoing call      |
| Alerting | Called when there is an incoming call going on      |
| InCall | Called multiple times while call is connected and running      |

## Events that are needed to get called to interact with the component

| Event        | Description           |
| ------------- |-------------:|
| answerCall      | Accept an incoming call when there is one ("Alerting") |
| placeCall      | Calls the defined destination URI      |
| hangupCall | Cancels outgoing or running Call      |
| toggleMicrophone | Toggles outgoing sound track (true / false). Used to mute outgoing sound.      |
| toggleVideo | Toggles outgoing video track (true / false). Used to mute outgoing video.      |


### Below is an example of how to embed the component in a React.js application

```javascript

  import WebRTCClient from "freepbx-react-webrtc";
  import EventEmitter from "events";

  constructor(props) {
    super(props)

    this.state = {
        eventHandler: new EventEmitter()
    };
  }
  
  // Default Events that are called by the WebRTCClient
  componentDidMount() {
    // Connected and Idle
    this.state.eventHandler.on("Idle", function() {
      // Do something...
    });

    // Outgoing call happening
    this.state.eventHandler.on("Calling", function() {
      // Do something...
    });

    // Incoming Call
    this.state.eventHandler.on("Alerting", function() {
      // Do something...
    });

    // Call started
    this.state.eventHandler.on("InCall", function() {
      // Do something...
    });
  }

  render() {
    return (
      <div className="App">
        //Video element used for rendering video self-view
        <video width="25%"  id="localVideo" autoPlay playsInline  muted="muted"></video>

        //Video element used for rendering video of remote party
        <video width="50%" id="remoteVideo" autoPlay playsInline ></video>

        <WebRTCClient
          enableVideo={true}
          enableSound={true}
          webSocketPort="8089"
          autoRegister = {true}
          sipDomain={sipDomain}
          sipServer={sipServer}
          sipUser={sipUser}
          sipPassword={sipPassword}
          destination={destinationUri}
          metaData={metaDataObject}
          alertVideoUrl="alertUrl"
          ringbackVideoUrl="ringbackUrl"
          localVideoTagId="localVideo"
          remoteVideoTagId="remoteVideo"
          skipStunServer={false}
          stunServerList=[{urls: "stun:stun.l.google.com:19302"}]
          eventHandler={this.state.eventHandler}
          eventHandlerEmit={this.eventHandlerEmit}
          updateCallState={this.updateCallState}
          updateConnectionState={this.updateConnectionState}
          traceSip={true}
        />
      </div>
    );
  }
}

```

| Parameter        | Description           |
| ------------- |-------------:|
| enableVideo | indicates if video should be enabled for calls |
| enableSound | indicates if sound should be enabled for calls |
| autoRegister | indicates if the component should send a SIP Register to be able to receive calls |
| sipDomain | is the SIP domain to be used for registration and calls |
| sipServer | This is an optional property indicating where to connect with the server. If this is not set the value of sipDomain is used |
| sipUser | is the user id to be used for authentication |
| destination | is the remote URI destination to be called |
| metaData | is an object to be passed to the remote side in a X-MetaData SIP header. The object is JSON stringified and then URL encoded before inserted as header value |
| alertVideoUrl | is an optional sring with an URL pointing to a video file supported by the  <video> element. This file is played when an inbound call is received. If the property is omitted a default file is played |
| ringbackVideoUrl | is an optional sring with an URL pointing to a video file supported by the  <video> element. This file is played when an call is placed until it has been answered. If the property is omitted a default file is played |
| skipStunServer | indicates if a STUN server should be used. Disable when only working in local network |
| stunServerList | list of Dicts with STUN Servers to use |
| eventHandler | EventHandler to communicate between the parent and child component |
| eventHandlerEmit | Function to pass to the WebRTCClient so it can emit events |
| updateCallState | Function that gets called with the CallState on change with a string as parameter |
| updateConnectionState | Function that gets called with the ConnectionState with a string as parameter |
| traceSip | Boolean to enable or disable advanced SIP output |

The component will try to play out audio and video feedback when calling and alerting incoming calls. Due to autoplay limitations and logic in different web browsers this may not play.
