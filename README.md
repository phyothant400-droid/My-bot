import logging
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, MessageHandler, filters, ContextTypes

# --- အချက်အလက်များ ---
BOT_TOKEN = "8694156894:AAFWbpmFBIX_aK4ReVgq-P_wTFX3vassy9w"
ADMIN_ID = 7115897772
MY_ACCOUNT_LINK = "https://t.me/PhyoThantNayNaing" # သင့် Link ကို ဒီမှာ ပြင်နိုင်ပါတယ်

logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    keyboard = [[InlineKeyboardButton("SIM တောင်းချင်လို့", callback_data='request_sim')]]
    reply_markup = InlineKeyboardMarkup(keyboard)
    await update.message.reply_text("👋 မင်္ဂလာပါ၊ KYC ဝန်ဆောင်မှုမှ ကြိုဆိုပါတယ်။\n\nSIM ကတ် တောင်းလိုပါက အောက်ကခလုတ်ကို နှိပ်ပါ။", reply_markup=reply_markup)

async def button_click(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    user = query.from_user
    await query.answer()

    if query.data == 'request_sim':
        await context.bot.send_message(
            chat_id=ADMIN_ID, 
            text=f"🔔 **SIM တောင်းဆိုမှုအသစ်!**\n\n👤 Name: {user.first_name}\n🆔 User ID: `{user.id}`\n🔗 Username: @{user.username if user.username else 'မရှိပါ'}\n\n(Customer ဆီ SIM ပို့ရန် ဤစာကို Reply ပြန်ပေးပါ)"
        )
        await query.edit_message_text("✅ SIM တောင်းဆိုမှုကို Admin ဆီ ပို့လိုက်ပါပြီ။ ခေတ္တစောင့်ပေးပါ။")

    elif query.data == 'request_otp':
        await context.bot.send_message(
            chat_id=ADMIN_ID, 
            text=f"🔑 **OTP တောင်းဆိုမှု!**\n\n👤 Name: {user.first_name}\n🆔 User ID: `{user.id}`"
        )
        await query.edit_message_text("✅ OTP တောင်းဆိုမှုကို ပို့လိုက်ပါပြီ။ Admin ပြန်ပို့ပေးပါလိမ့်မယ်။")

    elif query.data == 'success':
        await query.edit_message_text(f"👍 ဟုတ်ကဲ့ပါ၊ လုပ်ငန်းစဉ်အားလုံး ပြီးဆုံးပါက အောက်ပါအကောင့်သို့ ပို့ပေးပါ။\n\nContact: {MY_ACCOUNT_LINK}")

    elif query.data == 'fail':
        keyboard = [[InlineKeyboardButton("OTP ပြန်တောင်းရန်", callback_data='request_otp')]]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await query.edit_message_text("❌ အဆင်မပြေပါက OTP ပြန်တောင်းနိုင်ပါသည်။", reply_markup=reply_markup)

async def handle_admin_reply(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if update.message.chat_id == ADMIN_ID and update.message.reply_to_message:
        try:
            # စာသားထဲက ID ကို ရှာတယ်
            original_text = update.message.reply_to_message.text
            customer_id = int(original_text.split("User ID: ")[1].split("\n")[0].strip("`"))
            
            msg_text = update.message.text
            keyboard = [[InlineKeyboardButton("OTP
