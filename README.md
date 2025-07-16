from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, CommandHandler, filters, ContextTypes
import requests
import os

TELEGRAM_TOKEN = os.environ["7532910236:AAEO4IILuS_iLdmnXUBAov2hyx0LGY3dK-o"]
OPENROUTER_API_KEY = os.environ["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"]

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Привет! Я теперь умный бот с ИИ 🤖")

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_message = update.message.text
    print("Пришло сообщение:", user_message)

    response = requests.post(
        "https://openrouter.ai/api/v1/chat/completions",
        headers={
            "Authorization": f"Bearer {OPENROUTER_API_KEY}",
            "Content-Type": "application/json"
        },
        json={
            "model": "mistralai/mistral-7b-instruct",
            "messages": [
                {"role": "system", "content": "Отвечай дружелюбно и вежливо."},
                {"role": "user", "content": user_message}
            ]
        }
    )

    result = response.json()
    ai_reply = result["choices"][0]["message"]["content"]

    await update.message.reply_text(ai_reply)

app = ApplicationBuilder().token(TELEGRAM_TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.TEXT & (~filters.COMMAND), handle_message))

print("Бот с ИИ запущен. Ждёт сообщений...")
app.run_polling()
