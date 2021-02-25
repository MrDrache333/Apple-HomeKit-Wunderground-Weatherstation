# Apple-HomeKit-Wunderground-Weatherstation
<div>
  <img src="https://github.com/MrDrache333/Apple-HomeKit-Wunderground-Weatherstation/blob/main/homekit.jpg?raw=true" height=200px>
  <img src="https://github.com/MrDrache333/Apple-HomeKit-Wunderground-Weatherstation/blob/main/wunderground.png?raw=true" height=200px>
</div>
This represents a Solution to add any Wunderground Wetterstation to HomeKit using ioBroker and Node-Red.

## Requirements
- Running ioBroker Instance
- Installed [Node-RED Adapter](https://github.com/ioBroker/ioBroker.node-red)
- Installed [YaHKa-Adapter](https://github.com/jensweigele/ioBroker.yahka)
- [Configured Weatherstation ID](https://www.wunderground.com/member/devices) to Share Data with [Weather Underground](https://www.wunderground.com) or ID from any Station to use
- Wunderground [API-Key](https://www.wunderground.com/member/api-keys)

## Create objects in ioBroker
Please create the following datapoints in ioBroker

```
0_userdata.0
  |- Yahka_Devices
     |- Wunderstation
        |- humidity (Percent)
        |- rain (Boolean)
        |- temperature (Float)
```

## Import example into Node-Red
Import the following nodes into Node-Red and change the objects to your needs
```json
[{"id":"992700d7.5ff3b8","type":"inject","z":"a82bdceb.4f65d","name":"Update","props":[{"p":"payload"}],"repeat":"600","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":120,"y":1740,"wires":[["5aa3e999.90a78"]]},{"id":"5aa3e999.90a78","type":"http request","z":"a82bdceb.4f65d","name":"WU-API","method":"GET","ret":"obj","paytoqs":false,"url":"https://api.weather.com/v2/pws/observations/current?stationId=PLACE_YOUR_STATION_ID_HERE&format=json&units=m&apiKey=PLACE_YOUR_API-KEY_HERE","tls":"","persist":false,"proxy":"","authType":"","x":300,"y":1740,"wires":[["412ce581.2c3944","8149b056.4134e8","bb4431e3.f0a2f","242ac7dc.b0db88"]]},{"id":"412ce581.2c3944","type":"function","z":"a82bdceb.4f65d","name":"parsen","func":"var data = msg.payload\nif (isObject(data)){\n    var station = data.observations[0]\n    var temp = station.metric.temp\n    var hum = station.humidity\n    \n    var rain = station.metric.precipRate\n    rain = rain > 0\n    \n    msg.CurrentTemperature = temp\n    msg.CurrentRelativeHumidity = hum\n    msg.Rain = rain\n    return msg\n}\n\n\n\nfunction isObject(obj) {\n  return obj === Object(obj);\n}","outputs":1,"noerr":0,"initialize":"","finalize":"","x":470,"y":1740,"wires":[["6be7d232.330664","e01f051c.c1d64","33e0b0ca.f5411"]]},{"id":"e01f051c.c1d64","type":"template","z":"a82bdceb.4f65d","name":"HomeKit-Template: Humidity","field":"payload","fieldType":"msg","format":"handlebars","syntax":"mustache","template":"{{CurrentRelativeHumidity}}","output":"json","x":730,"y":1680,"wires":[["6d4a60b5.d5a4a"]]},{"id":"33e0b0ca.f5411","type":"template","z":"a82bdceb.4f65d","name":"HomeKit-Template: Rain","field":"payload","fieldType":"msg","format":"handlebars","syntax":"mustache","template":"{{Rain}}","output":"json","x":710,"y":1740,"wires":[["6ef9afa9.039a48"]]},{"id":"6be7d232.330664","type":"template","z":"a82bdceb.4f65d","name":"HomeKit-Template: Temperature","field":"payload","fieldType":"msg","format":"handlebars","syntax":"mustache","template":"{{CurrentTemperature}}","output":"json","x":740,"y":1800,"wires":[["1f5260d5.f12847"]]},{"id":"6d4a60b5.d5a4a","type":"ioBroker out","z":"a82bdceb.4f65d","name":"","topic":"0_userdata.0.Yahka_Devices.Wunderstation.humidity","ack":"true","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1130,"y":1680,"wires":[]},{"id":"6ef9afa9.039a48","type":"ioBroker out","z":"a82bdceb.4f65d","name":"","topic":"0_userdata.0.Yahka_Devices.Wunderstation.rain","ack":"true","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1050,"y":1740,"wires":[]},{"id":"1f5260d5.f12847","type":"ioBroker out","z":"a82bdceb.4f65d","name":"","topic":"0_userdata.0.Yahka_Devices.Wunderstation.temperature","ack":"true","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1140,"y":1800,"wires":[]}]
```
