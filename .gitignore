import telebot

import config
import random

from telebot import types
from mozg import get_map_cell

bot = telebot.TeleBot('5778144233:AAGbam72YVpjpPT0Z5mw2_lYb5ECh6kEqA8')
item = {}
cols, rows = 8, 8

gameIsStart = False

gameGround = [" ", " ", " ",
              " ", " ", " ",
              " ", " ", " ", ]

CrossesOrToe = ["0", "X"]

playerSymbol = CrossesOrToe[random.randint(0, 1)]

botSymbol = ""
if (playerSymbol == "0"):
    botSymbol = "X"
else:
    botSymbol = "0"

print("Bot is start")

# lose/win

winbool = False

losebool = False


def clear():
    global gameGround
    gameGround = [" ", " ", " ",
                  " ", " ", " ",
                  " ", " ", " ", ]


def win(cell_1, cell_2, cell_3):
    if cell_1 == playerSymbol and cell_2 == playerSymbol and cell_3 == playerSymbol:
        print("win")
        global winbool
        winbool = True


def lose(cell_1, cell_2, cell_3):
    if cell_1 == botSymbol and cell_2 == botSymbol and cell_3 == botSymbol:
        print("lose")
        global losebool
        losebool = True


def defend(cell_1, cell_2, posDef):
    if cell_1 == playerSymbol and cell_2 == playerSymbol:
        posDef = botSymbol


keyboard = telebot.types.InlineKeyboardMarkup()
keyboard.row(telebot.types.InlineKeyboardButton('⬅', callback_data='left'),
             telebot.types.InlineKeyboardButton('⬆', callback_data='up'),
             telebot.types.InlineKeyboardButton('⬇', callback_data='down'),
             telebot.types.InlineKeyboardButton('➡', callback_data='right'))
# ➡️⬅️⬆️⬇️
maps = {}


def get_map_str(map_cell, player):
    map_str = ""
    for y in range(rows * 2 - 1):
        for x in range(cols * 2 - 1):
            if map_cell[x + y * (cols * 2 - 1)]:
                map_str += "⬛"
            elif (x, y) == player:
                map_str += "🔸"
            else:
                map_str += "⬜"
        map_str += "\n"

    return map_str


@bot.message_handler(commands=['play'])
def play_message(message):
    map_cell = get_map_cell(cols, rows)

    user_data = {
        'map': map_cell,
        'x': 0,
        'y': 0
    }

    maps[message.chat.id] = user_data

    bot.send_message(message.from_user.id, get_map_str(map_cell, (0, 0)), reply_markup=keyboard)


@bot.callback_query_handler(func=lambda call: True)
def callback_func(query):
    user_data = maps[query.message.chat.id]
    new_x, new_y = user_data['x'], user_data['y']

    if query.data == 'left':
        new_x -= 1
    if query.data == 'right':
        new_x += 1
    if query.data == 'up':
        new_y -= 1
    if query.data == 'down':
        new_y += 1

    if new_x < 0 or new_x > 2 * cols - 2 or new_y < 0 or new_y > rows * 2 - 2:
        return None
    if user_data['map'][new_x + new_y * (cols * 2 - 1)]:
        return None

    user_data['x'], user_data['y'] = new_x, new_y

    if new_x == cols * 2 - 2 and new_y == rows * 2 - 2:
        bot.edit_message_text(chat_id=query.message.chat.id,
                              message_id=query.message.id,
                              text="Вы выиграли ☑")
        return None

    bot.edit_message_text(chat_id=query.message.chat.id,
                          message_id=query.message.id,
                          text=get_map_str(user_data['map'], (new_x, new_y)),
                          reply_markup=keyboard)


@bot.message_handler(commands=['start'])
def welcome(message):
    # keyboard
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item[0] = types.KeyboardButton("Рандомное число")
    item[1] = types.KeyboardButton("Как дела?")
    item[2] = types.KeyboardButton("Крестики нолики")
    markup.add(item[0], item[1], item[2])

    if message.text == "/start":
        bot.send_message(message.chat.id,
                         "Привет,{0.first_name}!,я новый телеграм бот у меня есть всякие команды)".format(
                             message.from_user, bot.get_me()),
                         parse_mode='html', reply_markup=markup)

    @bot.message_handler(commands=['help'])
    def send_welcome(message):
        bot.reply_to(message, "Привет, вижу тебе нужна помощь. На данный момент работают такие команды как: /start, "
                              "/help, /play, /stop, id, Hello, username. ")

    @bot.message_handler(commands=['stop'])
    def stop_command(message):
        bot.reply_to(message, "Понял принял, вырубаюсь")
        bot.stop_polling()

    @bot.message_handler()
    def get_user_test(message):
        if message.text == 'Hello':
            bot.send_message(message.chat.id, "И вам добрый день", parse_mode='html')
        elif message.text == 'id':
            bot.send_message(message.chat.id, f"Твой ID: {message.from_user.id}", parse_mode='html')
        elif message.text == 'username':
            bot.send_message(message.chat.id, f"Твой юзернейм: {message.from_user.username}", parse_mode='html')


@bot.message_handler(content_types=['text'])
def mess(message):
    if message.chat.type == 'private':
        if message.text == "Рандомное число":
            bot.send_message(message.chat.id, str(random.randint(0, 10000)))
        elif message.text == "Как дела?":
            bot.send_message(message.chat.id, "Хорошо)")
        elif message.text == "Крестики нолики":
            global gameIsStart
            gameIsStart = True
        else:
            bot.send_message(message.chat.id, "Я не знаю таких слов :(")

    # game(хрести-ноли)
    if gameIsStart == True:

        item = {}
        bot.send_message(message.chat.id, "Игра началась")

        global markup
        markup = types.InlineKeyboardMarkup(row_width=3)

        i = 0

        for i in range(9):
            item[i] = types.InlineKeyboardButton(gameGround[i], callback_data=str(i))

        markup.row(item[0], item[1], item[2])
        markup.row(item[3], item[4], item[5])
        markup.row(item[6], item[7], item[8])
        bot.send_message(message.chat.id, "Выбери клетку", reply_markup=markup)


@bot.callback_query_handler(func=lambda call: True)
def callbackInline(call):
    if (call.message):

        # bot manager
        randomCell = random.randint(0, 8)
        if gameGround[randomCell] == playerSymbol:
            randomCell = random.randint(0, 8)
        if gameGround[randomCell] == botSymbol:
            randomCell = random.randint(0, 8)
        if gameGround[randomCell] == " ":
            gameGround[randomCell] = botSymbol
        # player manager
        for i in range(9):
            if call.data == str(i):
                if (gameGround[i] == " "):
                    gameGround[i] = playerSymbol

            # lose or win
            win(gameGround[0], gameGround[1], gameGround[2])
            win(gameGround[0], gameGround[4], gameGround[8])
            win(gameGround[6], gameGround[4], gameGround[2])
            win(gameGround[6], gameGround[7], gameGround[8])
            win(gameGround[0], gameGround[3], gameGround[6])
            lose(gameGround[0], gameGround[1], gameGround[2])
            lose(gameGround[0], gameGround[4], gameGround[8])
            lose(gameGround[6], gameGround[4], gameGround[2])
            lose(gameGround[6], gameGround[7], gameGround[8])
            lose(gameGround[0], gameGround[3], gameGround[6])

            item[i] = types.InlineKeyboardButton(gameGround[i], callback_data=str(i))

        bot.edit_message_text(chat_id=call.message.chat.id, message_id=call.message.message_id, text="Крестики нолики",
                              reply_markup=None)
        # update cells
        global markup
        markup.row(item[0], item[1], item[2])
        markup.row(item[3], item[4], item[5])
        markup.row(item[6], item[7], item[8])

        bot.send_message(call.message.chat.id, "Выбери клетку", reply_markup=markup)
        global winbool
        if winbool:
            clear()
            bot.send_message(call.message.chat.id, "Я проиграл :(")

            winbool = False
            gameIsStart = False
        global losebool
        if losebool:
            clear()
            bot.send_message(call.message.chat.id, "Я выиграл!!")

            losebool = False
            gameIsStart = False
bot.polling(none_stop=True)
