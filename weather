const axios = require('axios');
const schedule = require('node-schedule');

const fetchWeatherAndAqi = async (lat, lon) => {
    console.log(`Fetching weather and AQI for coordinates ${lat}, ${lon}...`);
    const airvisualUrl = `http://api.airvisual.com/v2/nearest_city?lat=${lat}&lon=${lon}&key=d7ea92de-7993-4b87-8cb1-a1da9604c5c4`;

    try {
        const response = await axios.get(airvisualUrl);
        const data = response.data.data;
        const aqi = data.current.pollution.aqius;
        const temperature = data.current.weather.tp;
        const weatherConditionIcon = data.current.weather.ic;
        const weatherDescription = getWeatherDescription(weatherConditionIcon);
        console.log(`AQI: ${aqi}, Temperature: ${temperature}°C, Weather: ${weatherDescription}`);
        return { aqi, temperature, weatherDescription };
    } catch (error) {
        console.log('Unable to fetch data');
        return { aqi: null, temperature: null, weatherDescription: null };
    }
};

const getWeatherDescription = (iconCode) => {
    const descriptions = {
        "01d": "ท้องฟ้าแจ่มใส",
        "01n": "ท้องฟ้าแจ่มใส (กลางคืน)",
        "02d": "เมฆเล็กน้อย",
        "02n": "เมฆเล็กน้อย (กลางคืน)",
        "03d": "เมฆกระจาย",
        "03n": "เมฆกระจาย (กลางคืน)",
        "04d": "เมฆเป็นส่วนมาก",
        "04n": "เมฆเป็นส่วนมาก (กลางคืน)",
        "09d": "ฝนตกเล็กน้อย",
        "09n": "ฝนตกเล็กน้อย (กลางคืน)",
        "10d": "ฝนตก",
        "10n": "ฝนตก (กลางคืน)",
        "11d": "พายุฝนฟ้าคะนอง",
        "11n": "พายุฝนฟ้าคะนอง (กลางคืน)",
        "13d": "หิมะ",
        "13n": "หิมะ (กลางคืน)",
        "50d": "หมอก",
        "50n": "หมอก (กลางคืน)"
    };
    return descriptions[iconCode] || "สภาพอากาศไม่ทราบ";
};

const sendNotification = async (location, aqi, temperature, weatherDescription) => {
    if (aqi !== null && temperature !== null && weatherDescription !== null) {
        console.log(`Sending notification for ${location}...`);
        const lineNotifyToken = 'aYiCJJNFez4ctYJ6o0nV9peZkgVivICWjHZFUb5mevl';
        const lineUrl = 'https://notify-api.line.me/api/notify';
        const headers = {
            'Content-Type': 'application/x-www-form-urlencoded',
            'Authorization': `Bearer ${lineNotifyToken}`
        };
        const message = `${location} สภาพอากาศ: AQI ${aqi}, อุณหภูมิ ${temperature}°C, ${weatherDescription}`;

        try {
            await axios.post(lineUrl, `message=${encodeURIComponent(message)}`, { headers });
            console.log(`Notification for ${location} sent successfully`);
        } catch (error) {
            console.log(`Failed to send notification for ${location}. Error: ${error}`);
        }
    } else {
        console.log(`No data available for sending for ${location}`);
    }
};

const job = async () => {
    console.log("Starting job...");
    // Si Racha
    const { aqi: siRachaAqi, temperature: siRachaTemperature, weatherDescription: siRachaWeather } = await fetchWeatherAndAqi(13.1737, 100.9311);
    await sendNotification("ศรีราชา", siRachaAqi, siRachaTemperature, siRachaWeather);

    // Bang Lamung
    const { aqi: bangLamungAqi, temperature: bangLamungTemperature, weatherDescription: bangLamungWeather } = await fetchWeatherAndAqi(12.9276, 100.8771);
    await sendNotification("บางละมุง", bangLamungAqi, bangLamungTemperature, bangLamungWeather);
};

// Run immediately for testing, then schedule every hour
job();
const rule = new schedule.RecurrenceRule();
rule.minute = 0; // Runs at the beginning of each hour
schedule.scheduleJob(rule, job);
