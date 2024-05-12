import requests
import json
import time

# Укажи свой токен бота
TOKEN = '7172404187:AAFuaT8vpd3G4BS4eop34Sj8vNtLsooY3Jw'
# Укажи своё имя пользователя бота
BOT_USERNAME = 'QBit_BOT_TOKEN'

# URL для отправки сообщений через API Telegram
SEND_MESSAGE_URL = f"https://api.telegram.org/bot{TOKEN}/sendMessage"
EDIT_MESSAGE_URL = f"https://api.telegram.org/bot{TOKEN}/editMessageText"

# Общие данные о криптовалюте
class Cryptocurrency:
    name = "QBit"
    click_unit = 0.01

# Глобальные переменные для хранения баланса и списка рефералов
balance = {}
referrals = {}
user_tokens = {}

# Функция для отправки сообщений
def send_message(chat_id, text, reply_markup=None, parse_mode=None):
    params = {'chat_id': chat_id, 'text': text, 'reply_markup': reply_markup, 'parse_mode': parse_mode}
    response = requests.post(SEND_MESSAGE_URL, json=params)
    return response.json()

# Функция для редактирования сообщения
def edit_message(chat_id, message_id, text, reply_markup=None, parse_mode=None):
    params = {'chat_id': chat_id, 'message_id': message_id, 'text': text, 'reply_markup': reply_markup, 'parse_mode': parse_mode}
    response = requests.post(EDIT_MESSAGE_URL, json=params)
    return response.json()

# Обработчик команды /start
def handle_start(chat_id):
    balance[chat_id] = 0
    referrals[chat_id] = []
    user_tokens[chat_id] = 0
    message = "Вас приветствует виртуальная машина QuantumBit! Название токена: {}. Единица клика: {}. Ваш баланс: {}.".format(Cryptocurrency.name, Cryptocurrency.click_unit, balance[chat_id])
    send_message(chat_id, message, reply_markup=create_keyboard(chat_id))

# Обработчик команды /mine
def handle_mine(chat_id, message_id):
    global balance
    global user_tokens
    balance[chat_id] += Cryptocurrency.click_unit
    user_tokens[chat_id] += Cryptocurrency.click_unit
    message = f"Текущий баланс: {balance[chat_id]:.8f} {Cryptocurrency.name}\n\nНажмите кнопку 'Mine', чтобы добыть еще."
    edit_message(chat_id, message_id, message, reply_markup=create_keyboard(chat_id))

# Обработчик команды /invite
def handle_invite(chat_id):
    referral_link = f"https://t.me/{BOT_USERNAME}?start={chat_id}"
    message = f"Пригласите своих друзей по ссылке:\n{referral_link}"
    send_message(chat_id, message)

# Обработчик команды /tokens
def handle_tokens(chat_id):
    message = f"Ваш баланс токенов: {user_tokens[chat_id]:.8f} {Cryptocurrency.name}"
    send_message(chat_id, message)

# Создание клавиатуры с кнопками "Mine", "Invite" и "Tokens"
def create_keyboard(chat_id):
    keyboard = {
        'keyboard': [[{'text': 'Mine'}, {'text': 'Invite'}, {'text': 'Tokens'}]],
        'resize_keyboard': True
    }
    return json.dumps(keyboard)

# Обработка входящих сообщений и callback_query
def handle_message(update):
    if 'message' in update:
        message = update['message']
        chat_id = message['chat']['id']
        text = message.get('text')
        message_id = message['message_id']
        if text == '/start':
            handle_start(chat_id)
        elif text == '/mine':
            handle_mine(chat_id, message_id)
        elif text == '/invite':
            handle_invite(chat_id)
        elif text == '/tokens':
            handle_tokens(chat_id)
    elif 'callback_query' in update:
        callback_query = update['callback_query']
        chat_id = callback_query['message']['chat']['id']
        message_id = callback_query['message']['message_id']
        data = callback_query['data']
        if data == 'mine':
            handle_mine(chat_id, message_id)
        elif data == 'invite':
            handle_invite(chat_id)

# Функция для подсчета количества рефералов
def count_referrals():
    counts = {}
    for creator, invited_users in referrals.items():
        counts[creator] = len(invited_users)
    return counts

# Главная функция, которая будет запускать бота

def main():
    offset = None
    while True:
        try:
            response = requests.get(f"https://api.telegram.org/bot{TOKEN}/getUpdates", params={'offset': offset})
            updates = response.json()['result']
            for update in updates:
                handle_message(update)
                offset = update['update_id'] + 1
        except Exception as e:
            print("Ошибка:", e)
            time.sleep(1)

if __name__ == "__main__":
    main()