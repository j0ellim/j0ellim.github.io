# DHT11 sensor module with servo motor

----
https://j0ellim.github.io



Fans are quintessential in Singapore due to it being located on the equator and its high humidity. They are increasingly important during the months of May to July, due to the existing gap between the northern and southern moonsoon which reduces the amount of rain and wind we get. Wouldn’t it be perfect to constantly have your fan on at all times? However in reality where we are all focused on being more sustainable and eco friendly,it is best to only turn on the fan when necessary like in the daytime. In this guide, I'll be showing you a simple process that you can follow through to make use of a motor that turns on according to the temperature and humidity:


##### You will require this few items: 
1. ESP32-C3-DevKitM-1 / Arduino uno 
2. Small Breadboard
3. DHT11 Sensor Module
4. Micro Servo Motor SG90 
5. 6 x jumper wires

----
### DHT11 Sensor Module
![IMG_8197](https://github.com/j0ellim/j0ellim.github.io/assets/118902010/04d0b8ce-5046-4ee7-9515-dc1dab963867)
DHT11 sensors module has 3 pins, + , out and -. 

+: VCC Power supply 3.5V to 5.5V

Out: Data Outputs both Temperature and Humidity through serial Data which goes to GPIO pin (General-Purpose Input/Output)

-: Ground Connected to the ground of the circuit


| Specifications         |                                  |
| ----------------------:| --------------------------------:|
| Operating voltage      | 3.5V-5V                          |
| Operating current      | 0.3mA (measuring) 60uA (standby) |
| output                 | Serial data                      |
|Temperature Range       | 0°C to 50°C                      |
|Humidity Range          | 20% to 90%                       |
|Accuracy                | ±1°C and ±1%                     |


---
### Micro Servo Motor SG90 

![IMG_8198](https://github.com/j0ellim/j0ellim.github.io/assets/118902010/678bb489-0158-45df-8a04-57dd6b5ab019)

Servo motors have 3 wires of different colours primarily being brown, red and orange. 
-  orange:pwm (pulse width modulation) any pwm pin
-  red:vcc
-  brown:ground

  
| Specifications         |                                       |
| ----------------------:| -------------------------------------:|
| Speed                  | 0.1 sec                               |
| Voltage                | 4.8V-6V                               |
|(RX) Required Pulse     | 3.3 ~ 5 Volt Peak to Peak Square Wave |


----
### Schematics 
 <img width="956" alt="Screenshot 2024-03-10 at 12 27 15 PM" src="https://github.com/j0ellim/j0ellim.github.io/assets/118902010/0322e4b2-d692-42f1-b1c0-fff8f7c74832">
eap32-c3 devkitm-1 image from : https://docs.espressif.com/projects/esp-idf/en/stable/esp32c3/hw-reference/esp32c3/user-guide-devkitm-1.html

---

### Youtube video of it working
https://youtu.be/wp9mn9pgZ-s

---
### Code for setting up DHT11 sensor module and servo motor

The following code below is for setting up the DHT11 sensor module and the servo motor. It is entirely up to you to change the conditions for which the servo motor should spin. 



```

#include <DHT.h>
#include <Servo.h>

DHT dht(4,DHT11);

Servo myservo= Servo();  // Create servo object

// twelve servo objects can be created on most boards

//int pos = 0;    // variable to store the servo position

void setup() {
  // put your setup code here, to run once:
  dht.begin();
  //delay(2000);
 
  myservo.attach(2); // Attach the servo to pin 2
  Serial.begin(115200);
}

void loop() {
  // put your main code here, to run repeatedly:
  float temp = dht.readTemperature();
  float humidity = dht.readHumidity();
  Serial.print("Temp: ");
  Serial.print(temp);
  Serial.print("C");
  Serial.println("--------");
  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.print("%");
  Serial.print("--------");
  //delay(2000);

  if (temp > 27) { 
    for (int pos = 0; pos <= 360; pos++) {  // go from 0-180 degrees
    myservo.write(2, pos);        // set the servo position (degrees)
    delay(1);
  }
  for (int pos = 360; pos >= 0; pos--) {  // go from 180-0 degrees
    myservo.write(2, pos);        // set the servo position (degrees)
    delay(1);
  }

  } else if (humidity > 70) { 
    for (int pos = 0; pos <= 180; pos++) {  // go from 0-180 degrees
    myservo.write(2, pos);        // set the servo position (degrees)
    delay(1);
  }
  for (int pos = 180; pos >= 0; pos--) {  // go from 180-0 degrees
    myservo.write(2, pos);        // set the servo position (degrees)
    delay(15);
  }
  } 
}


```


---
### Firebase realtime database
The following code is to set the firebase realtime database connection up in your arduino ide. The code can also be found on the Arduino IDE library Firebase Arduino Client Library for ESP8266 and ESP32 by Mobizt > Examples >RTDB > Basic. From there you would be required to enter your `Wifi SSID`,` Wifi password`, `API_key` from your firebase and your firebase realtime database URL, the user email and password and make changes to the code by including your DHT11 sensor, beginning it and sending data over to the Firebase. 



```

/**
 * Created by K. Suwatchai (Mobizt)
 *
 * Email: k_suwatchai@hotmail.com
 *
 * Github: https://github.com/mobizt/Firebase-ESP-Client
 *
 * Copyright (c) 2023 mobizt
 *
 */
#include <DHT.h>   //INCLUDE THIS IN 
DHT dht(4,DHT11);  //INCLUDE THIS IN
#include <Arduino.h>
#if defined(ESP32) 
#include <WiFi.h>
#elif defined(ESP8266)
#include <ESP8266WiFi.h>
#elif __has_include(<WiFiNINA.h>)
#include <WiFiNINA.h>
#elif __has_include(<WiFi101.h>)
#include <WiFi101.h>
#elif __has_include(<WiFiS3.h>)
#include <WiFiS3.h>
#endif

#include <Firebase_ESP_Client.h>

// Provide the token generation process info.
#include <addons/TokenHelper.h>

// Provide the RTDB payload printing info and other helper functions.
#include <addons/RTDBHelper.h>

/* 1. Define the WiFi credentials */
#define WIFI_SSID "iPhone(94)"      //INCLUDE THIS IN
#define WIFI_PASSWORD "123456789"   //INCLUDE THIS IN 

// For the following credentials, see examples/Authentications/SignInAsUser/EmailPassword/EmailPassword.ino

/* 2. Define the API Key */
#define API_KEY "AIzaSyCpHSGfwuQlPu-WoYjzk2mBud9DhJIfmko"  //INCLUDE THIS IN

/* 3. Define the RTDB URL */
#define DATABASE_URL "https://fir-assignment-f9ad1-default-rtdb.asia-southeast1.firebasedatabase.app" //INCLUDE THIS IN BUT REMOVE THE LAST / 

/* 4. Define the user Email and password that alreadey registerd or added in your project */
#define USER_EMAIL "limjoel25@gmail.com"      //INCLUDE THIS IN 
#define USER_PASSWORD "Home2545"              //INCLUDE THIS IN 

// Define Firebase Data object
FirebaseData fbdo;

FirebaseAuth auth;
FirebaseConfig config;

unsigned long sendDataPrevMillis = 0;

unsigned long count = 0;

#if defined(ARDUINO_RASPBERRY_PI_PICO_W)
WiFiMulti multi;
#endif

void setup()
{

  Serial.begin(115200);
  dht.begin();                         //INCLUDE THIS IN 
#if defined(ARDUINO_RASPBERRY_PI_PICO_W)
  multi.addAP(WIFI_SSID, WIFI_PASSWORD);
  multi.run();
#else
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
#endif

  Serial.print("Connecting to Wi-Fi");
  unsigned long ms = millis();
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(300);
#if defined(ARDUINO_RASPBERRY_PI_PICO_W)
    if (millis() - ms > 10000)
      break;
#endif
  }
  Serial.println();
  Serial.print("Connected with IP: ");
  Serial.println(WiFi.localIP());
  Serial.println();

  Serial.printf("Firebase Client v%s\n\n", FIREBASE_CLIENT_VERSION);

  /* Assign the api key (required) */
  config.api_key = API_KEY;

  /* Assign the user sign in credentials */
  auth.user.email = USER_EMAIL;
  auth.user.password = USER_PASSWORD;

  /* Assign the RTDB URL (required) */
  config.database_url = DATABASE_URL;

  /* Assign the callback function for the long running token generation task */
  config.token_status_callback = tokenStatusCallback; // see addons/TokenHelper.h

  // Or use legacy authenticate method
  // config.database_url = DATABASE_URL;
  // config.signer.tokens.legacy_token = "<database secret>";

  // To connect without auth in Test Mode, see Authentications/TestMode/TestMode.ino

  //////////////////////////////////////////////////////////////////////////////////////////////
  // Please make sure the device free Heap is not lower than 80 k for ESP32 and 10 k for ESP8266,
  // otherwise the SSL connection will fail.
  //////////////////////////////////////////////////////////////////////////////////////////////

  // Comment or pass false value when WiFi reconnection will control by your code or third party library e.g. WiFiManager
  Firebase.reconnectNetwork(true);

  // Since v4.4.x, BearSSL engine was used, the SSL buffer need to be set.
  // Large data transmission may require larger RX buffer, otherwise connection issue or data read time out can be occurred.
  fbdo.setBSSLBufferSize(4096 /* Rx buffer size in bytes from 512 - 16384 */, 1024 /* Tx buffer size in bytes from 512 - 16384 */);

  // Limit the size of response payload to be collected in FirebaseData
  fbdo.setResponseSize(2048);

  Firebase.begin(&config, &auth);

  // The WiFi credentials are required for Pico W
  // due to it does not have reconnect feature.
#if defined(ARDUINO_RASPBERRY_PI_PICO_W)
  config.wifi.clearAP();
  config.wifi.addAP(WIFI_SSID, WIFI_PASSWORD);
#endif

  Firebase.setDoubleDigits(5);

  config.timeout.serverResponse = 10 * 1000;

  // You can use TCP KeepAlive in FirebaseData object and tracking the server connection status, please read this for detail.
  // https://github.com/mobizt/Firebase-ESP-Client#about-firebasedata-object
  // fbdo.keepAlive(5, 5, 1);

  /** Timeout options.

  //Network reconnect timeout (interval) in ms (10 sec - 5 min) when network or WiFi disconnected.
  config.timeout.networkReconnect = 10 * 1000;

  //Socket connection and SSL handshake timeout in ms (1 sec - 1 min).
  config.timeout.socketConnection = 10 * 1000;

  //Server response read timeout in ms (1 sec - 1 min).
  config.timeout.serverResponse = 10 * 1000;

  //RTDB Stream keep-alive timeout in ms (20 sec - 2 min) when no server's keep-alive event data received.
  config.timeout.rtdbKeepAlive = 45 * 1000;

  //RTDB Stream reconnect timeout (interval) in ms (1 sec - 1 min) when RTDB Stream closed and want to resume.
  config.timeout.rtdbStreamReconnect = 1 * 1000;

  //RTDB Stream error notification timeout (interval) in ms (3 sec - 30 sec). It determines how often the readStream
  //will return false (error) when it called repeatedly in loop.
  config.timeout.rtdbStreamError = 3 * 1000;

  Note:
  The function that starting the new TCP session i.e. first time server connection or previous session was closed, the function won't exit until the
  time of config.timeout.socketConnection.

  You can also set the TCP data sending retry with
  config.tcp_data_sending_retry = 1;

  */
}

void loop()
{

  // Firebase.ready() should be called repeatedly to handle authentication tasks.

  if (Firebase.ready() && (millis() - sendDataPrevMillis > 15000 || sendDataPrevMillis == 0))
  {
    sendDataPrevMillis = millis();

    float temp = dht.readTemperature();          //CODE TO READ THE TEMP 
    float humidity = dht.readHumidity();         //CODE TO READ THE HUMIDITY
    
    // CODE TO SET TEMPERATURE IN FIREBASE
    Serial.printf("Set temperature... %s\n", Firebase.RTDB.setString(&fbdo, F("/Weather/temperature"), String(temp)) ? "ok" : fbdo.errorReason().c_str());                  

    // CODE TO GET THE TEMPERATURE FROM FIREBASE AND PRINT IT 
    Serial.printf("Get temperature... %s\n", Firebase.RTDB.getString(&fbdo, F("/Weather/temperature")) ? fbdo.to<const char *>() : fbdo.errorReason().c_str());

    // CODE TO SET THE HUMIDITY IN FIREBASE 
    Serial.printf("Set humidity... %s\n", Firebase.RTDB.setString(&fbdo, F("/Weather/humidity"), String(humidity)) ? "ok" : fbdo.errorReason().c_str());

    // CODE TO GET THE HUMIDITY FROM FIREBASE AND PRINT IT 
    Serial.printf("Get humidity... %s\n", Firebase.RTDB.getString(&fbdo, F("/Weather/humidity")) ? fbdo.to<const char *>() : fbdo.errorReason().c_str());
    
    Serial.println();

    // For generic set/get functions.

    // For generic set, use Firebase.RTDB.set(&fbdo, <path>, <any variable or value>)

    // For generic get, use Firebase.RTDB.get(&fbdo, <path>).
    // And check its type with fbdo.dataType() or fbdo.dataTypeEnum() and
    // cast the value from it e.g. fbdo.to<int>(), fbdo.to<std::string>().

    // The function, fbdo.dataType() returns types String e.g. string, boolean,
    // int, float, double, json, array, blob, file and null.

    // The function, fbdo.dataTypeEnum() returns type enum (number) e.g. firebase_rtdb_data_type_null (1),
    // firebase_rtdb_data_type_integer, firebase_rtdb_data_type_float, firebase_rtdb_data_type_double,
    // firebase_rtdb_data_type_boolean, firebase_rtdb_data_type_string, firebase_rtdb_data_type_json,
    // firebase_rtdb_data_type_array, firebase_rtdb_data_type_blob, and firebase_rtdb_data_type_file (10)

    count++;
  }
}

/** NOTE:
 * When you trying to get boolean, integer and floating point number using getXXX from string, json
 * and array that stored on the database, the value will not set (unchanged) in the
 * FirebaseData object because of the request and data response type are mismatched.
 *
 * There is no error reported in this case, until you set this option to true
 * config.rtdb.data_type_stricted = true;
 *
 * In the case of unknown type of data to be retrieved, please use generic get function and cast its value to desired type like this
 *
 * Firebase.RTDB.get(&fbdo, "/path/to/node");
 *
 * float value = fbdo.to<float>();
 * String str = fbdo.to<String>();
 *
 */

/// PLEASE AVOID THIS ////

// Please avoid the following inappropriate and inefficient use cases
/**
 *
 * 1. Call get repeatedly inside the loop without the appropriate timing for execution provided e.g. millis() or conditional checking,
 * where delay should be avoided.
 *
 * Everytime get was called, the request header need to be sent to server which its size depends on the authentication method used,
 * and costs your data usage.
 *
 * Please use stream function instead for this use case.
 *
 * 2. Using the single FirebaseData object to call different type functions as above example without the appropriate
 * timing for execution provided in the loop i.e., repeatedly switching call between get and set functions.
 *
 * In addition to costs the data usage, the delay will be involved as the session needs to be closed and opened too often
 * due to the HTTP method (GET, PUT, POST, PATCH and DELETE) was changed in the incoming request.
 *
 *
 * Please reduce the use of swithing calls by store the multiple values to the JSON object and store it once on the database.
 *
 * Or calling continuously "set" or "setAsync" functions without "get" called in between, and calling get continuously without set
 * called in between.
 *
 * If you needed to call arbitrary "get" and "set" based on condition or event, use another FirebaseData object to avoid the session
 * closing and reopening.
 *
 * 3. Use of delay or hidden delay or blocking operation to wait for hardware ready in the third party sensor libraries, together with stream functions e.g. Firebase.RTDB.readStream and fbdo.streamAvailable in the loop.
 *
 * Please use non-blocking mode of sensor libraries (if available) or use millis instead of delay in your code.
 *
 * 4. Blocking the token generation process.
 *
 * Let the authentication token generation to run without blocking, the following code MUST BE AVOIDED.
 *
 * while (!Firebase.ready()) <---- Don't do this in while loop
 * {
 *     delay(1000);
 * }
 *
 */


```




---
### Code for Tkinter

To set up tkinter, you would require a json file which you can generate from your firebase ` generate private key ` and the web url of your firebase realtime database. Copy the code below into an IDE where u can run it. 

https://github.com/j0ellim/j0ellim.github.io/assets/118902010/df70023c-cb6d-4f69-a13e-d3cd0fd57fdf


``` 

import firebase_admin
from firebase_admin import credentials
from firebase_admin import auth
from firebase_admin import db

import tkinter as tk

cred=credentials.Certificate('fir-assignment-f9ad1-firebase-adminsdk-mouap-d01763678e.json')

firebase_admin.initialize_app(cred,
    {
        'databaseURL':'https://fir-assignment-f9ad1-default-rtdb.asia-southeast1.firebasedatabase.app'
    }
)

ref = db.reference('/Weather')
ref_temperature = db.reference('/Weather/temperature')
ref_humidity = db.reference('/Weather/humidity')



print(ref_temperature.get())
print(ref_humidity.get())


#create a window
window = tk.Tk()
window.title("Weather")
 

label = tk.Label(window)

label_temperature = tk.Label(window, text="Temperature: Loading...")
label_temperature.pack()

label_humidity = tk.Label(window, text="Humidity: Loading...")
label_humidity.pack()

# Function to update temperature and humidity labels from Firebase
def update_from_firebase():
    temperature = ref_temperature.get()
    humidity = ref_humidity.get()
    
    # Update the labels with the latest values
    label_temperature.config(text=f"Temperature: {temperature}°C")
    label_humidity.config(text=f"Humidity: {humidity}%")
    
    # Schedule the function to run again after 1000 milliseconds (1 second)
    window.after(1000, update_from_firebase)

# Start the update loop
update_from_firebase()



window.mainloop()



```

















