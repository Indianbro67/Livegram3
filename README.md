import logging
from telegram import Update, InputMediaPhoto, InputMediaDocument, InputMediaVideo
from telegram.ext import Updater, CommandHandler, MessageHandler
from telegram.ext.filters import Filters, Message, MediaGroup

# Set your bot token and owner user ID
BOT_TOKEN = '6157850456:AAECSQKBOrjcmF_mI9ngnltvsHqjV7TBOEk'
OWNER_USER_ID = 5841005593  # Replace with the actual owner's user ID

# Set up logging
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                    level=logging.INFO)
logger = logging.getLogger(__name__)

def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text('Hello! I am your feedback bot. Please send your feedback.')

def forward_feedback(update: Update, context: CallbackContext) -> None:
    user_id = update.message.from_user.id
    feedback_text = update.message.text
    feedback_media = update.message.media_group
    feedback_media_files = []

    if feedback_media:
        for media in feedback_media:
            if isinstance(media, (InputMediaPhoto, InputMediaDocument, InputMediaVideo)):
                feedback_media_files.append(media)

    if feedback_text or feedback_media_files:
        feedback_message = f"Feedback from user {user_id}:\n\n"
        if feedback_text:
            feedback_message += f"Text: {feedback_text}\n"
        if feedback_media_files:
            feedback_message += "Media:\n"
            for index, media in enumerate(feedback_media_files, start=1):
                feedback_message += f"{index}. Type: {media.type}\n"

        context.bot.send_message(OWNER_USER_ID, feedback_message)
        update.message.reply_text("Thank you for your feedback!")

def main():
    updater = Updater(BOT_TOKEN)

    dp = updater.dispatcher

    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(MessageHandler(Filters.text | Filters.media_group, forward_feedback))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
