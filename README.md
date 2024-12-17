import os
import requests
from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor
API_TOKEN = '7879667298:AAFTBhTg3PqXuxnsvHEEI1kURmHNB5CWDpY'
WEATHER_API_KEY = 'dc2d049c9482fcb70eda32c97b07b2bb'
bot = Bot(token=API_TOKEN)
dp = Dispatcher(bot)
@dp.message_handler(commands=['start'])
async def start_command(message: types.Message):
    await message.reply("Привет! Напиши название города, и я пришлю сводку погоды.")
@dp.message_handler()
async def get_weather(message: types.Message):
    city = message.text  # Получаем название города от пользователя
    weather_url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={WEATHER_API_KEY}&units=metric"
    response = requests.get(weather_url)
    if response.status_code == 200:
        data = response.json()
        temp = data['main']['temp']
        weather_description = data['weather'][0]['description']
        humidity = data['main']['humidity']  # Влажность
        wind_speed = data['wind']['speed']    # Скорость ветра
        pressure = data['main']['pressure']    # Атмосферное давление
        await message.reply(
            f"Погода в {city}:\n"
            f"Температура: {temp}°C\n"
            f"Описание: {weather_description}\n"
            f"Влажность: {humidity}%\n"
            f"Скорость ветра: {wind_speed} м/с\n"
            f"Атмосферное давление: {pressure} гПа"
        )
    else:
        await message.reply("Не удалось получить данные о погоде. Проверьте название города.")
if __name__ == '__main__':
    executor.start_polling(dp)
