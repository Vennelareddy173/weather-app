# weather-app
import streamlit as st
import requests

# API Setup
API_KEY = "8bcb77faeab207d8ee42e623f20d16ef"
BASE_URL = "https://api.openweathermap.org/data/2.5/weather"

# Get weather data
def get_weather(city):
    params = {
        "q": city,
        "appid": API_KEY,
        "units": "metric"
    }
    try:
        res = requests.get(BASE_URL, params=params)
        data = res.json()
        if res.status_code == 200:
            return {
                "city": data["name"],
                "temp": data["main"]["temp"],
                "desc": data["weather"][0]["description"].capitalize(),
                "icon": data["weather"][0]["icon"],
                "humidity": data["main"]["humidity"],
                "wind": data["wind"]["speed"]
            }
        else:
            return {"error": data.get("message", "Invalid city")}
    except Exception as e:
        return {"error": str(e)}

# Inject custom background using CSS
def set_background():
    st.markdown(
        """
        <style>
        .stApp {
            background-image: url("https://images.unsplash.com/photo-1503264116251-35a269479413");
            background-size: cover;
            background-repeat: no-repeat;
            background-attachment: fixed;
        }
        .block-container {
            background-color: rgba(255,255,255,0.8);
            padding: 2rem;
            border-radius: 15px;
        }
        </style>
        """,
        unsafe_allow_html=True
    )

# UI
set_background()
st.title("🌤️ Weather App ")
city = st.text_input("Enter city name")

if st.button("Get Weather"):
    if city:
        result = get_weather(city)
        if "error" in result:
            st.error(f"❌ {result['error']}")
        else:
            st.success(f"Weather in {result['city']}")
            icon_url = f"http://openweathermap.org/img/wn/{result['icon']}@2x.png"
            st.image(icon_url, width=100)
            st.metric("🌡️ Temperature", f"{result['temp']} °C")
            st.write(f"☁️ Description: {result['desc']}")
            st.write(f"💧 Humidity: {result['humidity']}%")
            st.write(f"🌬️ Wind Speed: {result['wind']} m/s")
    else:
        st.warning("⚠️ Please enter a city.")
