# work flow
app [{payload}] -> server -> server store the payload 


esp [check for update()] -> server -> if initiate update is available from app -> go to github and download .bin -> flash device
after flash esp -> update firmwire version in mongo 


# Schema
> ```js
> const TerralytSchema = new mongoose.Schema({
>   user: {
>     mongoId: { type: mongoose.Schema.Types.ObjectId, ref: 'users', required: true },
>   },
>   lastUpdated: { type:Date},
>   deviceCatagory:{type:String,required:true},
>   deviceId: {type:String,required: true},
>   deviceName:{type:String,required:true},
>   status:{type:String,default:'off'},
>   firmwareVersion:{type:String,default:'1.0.0'},
>   macAddress:{type:String},
>   lastUpdated:{type:Date},
>   sensors:{
>     sensor1:{
>       humidity:{type:Number,},
>       soilHardness:{type:Number,},
>       temperature:{type:Number,}
>     },
>     sensor2:{
>       humidity:{type:Number,},
>       soilHardness:{type:Number,},
>       temperature:{type:Number,}
>     },
>       charts: [{
>       timestamp:{type:String,required:true},
>       humidity:{type:Number,required:true},
>       temperature:{type:Number,required: true }
>     }]
>   }
> }, { versionKey: false,});
> ```

# MongoDB

> ```json
> {
>   "_id": {
>     "$oid": "697093b72b84d4c970c553c7"
>   },
>   "user": {
>     "mongoId": {
>       "$oid": "695d00845a95d7e777d03adb"
>     }
>   },
>   "lastUpdated": {
>     "$date": "2026-01-21T14:22:07.343Z"
>   },
>   "deviceCatagory": "Terralyt",
>   "deviceId": "101",
>   "deviceName": "Device",
>   "status": "off",
>   "firmwareVersion": "1.0.0",
>   "sensors": {
>     "sensor1": {
>       "humidity": 7,
>       "soilHardness": 12,
>       "temperature": 30
>     },
>     "sensor2": {
>       "humidity": 10,
>       "soilHardness": 8,
>       "temperature": 28
>     },
>     "charts": []
>   }
> }
> ```


# Server

app -> server
> ```js
> OTA_Update=[]
> app.post("/:deviceCategory/:deviceId/OTA", authenticateToken async (req, res) => {
> const { deviceId, targetFirmware } = req.body;
> OTA_Update.push({deviceId,targetFirmware })
> ----something like this--------
> });
> ```


ESP -> Server    Check For update
> ```js
> app.get("/hardware/devices/ota/check", async (req, res) => {
>    Verify_Hash()
>   /* find if the device is availble in OTA_Update array
>  if true then resposne back to esp to fetch file with firmware version*/
>  const firmwareUrl = `https://github.com/YOUR_ACCOUNT/YOUR_REPO/releases/download/v${targetFirmware}/firmware.bin`;
> });
> ```


> ```js
> app.get("/hardware/devices/ota/complete", async (req, res) => {
>   /* clear the device entry from OTA_Update array
>  and write mongo db with current firmware */
> });
> ```





# ESP
> ```js
> export const fetch_Update=async()=>{
>   try {
>       const res = await fetch(`http://YOUR_IP OR HOSTINGER/hardware/devices/ota/check`, {
>           method:"GET",
>           headers:{"Content-Type":"application/json"},
>       });
>   } catch (error) {
>     console.log(error)
>       console.log("🚀 \n login \n error:", error)
>       return {status:500}
>   }
> }
> ```


# App
> ```js
>  const triggerUpdate = async () => {
>    const res = await fetch("https://YOUR_IP/:deviceCategory/:deviceId/OTA", {
>      method: "POST",
>      headers: {"Content-Type": "application/json"},
>      body: JSON.stringify({deviceId, targetFirmware: version})
>    });
>
>    if (res.ok) Alert.alert("OTA Started");
>    else Alert.alert("Error");
>  };
> ```



