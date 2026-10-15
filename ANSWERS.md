# Data Pipeline Check #1 - Greenhouse

## batch102.csv

1. What information does this file record?**
Soil moisture and soil temperature for each bed.

2. What kind of device produced it?**
Soil sensors in the beds. Each row has a bed and a sensor ID, and they all read at the same time every 2 hours.

3. What identifies one record?**
Date, time and sensor ID together.

4. How are the fields represented?**
Values separated by commas, like 07/15/2026,2:00 AM,SN-4821,BED-1,27.0,79.2. The first row has the column names. The date is month first and the time uses AM/PM. Units are only in the column names, like _F for Fahrenheit.

5. How many records are there?**
120. There are 10 sensors and each reads 12 times.

6. What do you notice about how it collects and stores data?**
It only checks every 2 hours, so it can miss stuff in between. Each bed has 2 sensors that read about the same, so one is probably a backup. Beds 1 and 2 jump from about 27 to 33 at 6 AM, right after the watering file shows zone 1 watered at 5 AM. The soil dries out in the afternoon, and soil temp is highest around 6 PM.

7. Advantages and disadvantages of this format**
It's simple and opens in Excel. But a date like 07/08 could mean July 8 or August 7.

## feed102.xml

1. What information does this file record?**
Air temperature, humidity and wind speed.

2. What kind of device produced it?**
A weather station called WS-01. It measures wind, so it's probably outside.

3. What identifies one record?**
The date and time of each observation.

4. How are the fields represented?**
Each value is inside tags with its name, like a humidity tag with 74 in it, and the unit is written inside the tag (unit="percent"). The date is day first (15/07/2026) and the time is 24-hour.

5. How many records are there?**
144, one every 10 minutes all day.

6. What do you notice about how it collects and stores data?**
It never skips a reading. Temperature is in Celsius but wind is in mph, which is weird to mix. Humidity goes down as it gets hotter, from about 93% in the morning to about 44% in the afternoon.

7. Advantages and disadvantages of this format**
Every value says what it is and its unit. But it's really long, over 700 lines for 144 readings, and the date is day first unlike the other two files.

## stream102.json

1. What information does this file record?**
When each zone got watered, how fast the water flowed, and if the pump was on or off.

2. What kind of device produced it?**
The watering pump. It has pumpStatus and flowRateGpm (gallons per minute).

3. What identifies one record?**
Date and time. Only one zone waters at a time.

4. How are the fields represented?**
Each reading is a list of names and values, like "zone": 1, "flowRateGpm": 4.11, "pumpStatus": "on". Zone and flow are numbers, and pumpStatus is the word "on" or "off". The date and time are text.

5. How many records are there?**
38. There are 7 waterings: 3 in the morning with 7 readings each, 1 at 1 PM with 5, and 3 in the evening with 4 each.

6. What do you notice about how it collects and stores data?**
It only writes data while it's watering, every 5 minutes, then one "off" line and nothing until the next watering. The zones take turns, 1 then 2 then 3. Morning waterings are 30 minutes and evening ones are 15. The first reading is always a little lower, probably because the pump is starting up.

7. Advantages and disadvantages of this format**
It's short and the numbers are real numbers. But there are no units except in the name flowRateGpm, and when the pump is off there's no data at all.

# Checkpoint 2 - New Requirements

## Soil sensors now report battery level

1. Which file does it go in?**
batch102.csv. The battery is in the soil sensors, and this is the file where every row has a SensorID, so each reading can have its own battery level.

2. What did I name the field?**
BatteryLevel_Pct. I put the unit at the end of the name like SoilTemp_F, because this file only shows units in the column names.

3. How did I add it?**
I added BatteryLevel_Pct as a new column at the end of the header row, then put an extra comma at the end of all 120 rows so the value is blank.

## The weather station now tracks sunlight

1. Which file does it go in?**
feed102.xml. The weather station WS-01 makes this file, and it already records outside conditions every 10 minutes.

2. What did I name the field?**
sunlight. It's camelCase like airTemperature and windSpeed, and the unit goes inside the tag (unit="lux") like the other values.

3. How did I add it?**
I added an empty sunlight tag, <sunlight unit="lux"></sunlight>, right after windSpeed in all 144 observations.

## The water controller now records automatic or manual

1. Which file does it go in?**
stream102.json. This is the watering pump's log, and it already has pumpStatus, so how the pump got turned on fits here.

2. What did I name the field?**
activationMode. It's camelCase like flowRateGpm and pumpStatus. Later it would be the word "auto" or "manual", the same way pumpStatus is "on" or "off".

3. How did I add it?**
I added "activationMode": null after pumpStatus in all 38 records. I used null because that's how JSON shows an empty value.
