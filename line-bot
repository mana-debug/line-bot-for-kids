from linebot import LineBotApi, WebhookParser
from linebot.models import MessageEvent, TextMessage, TextSendMessage
from linebot.exceptions import InvalidSignatureError
from flask import Flask, request, abort
import openai
import os

#LINE botのチャンネルアクセストークンとチャンネルシークレットを環境変数から取得する
line_bot_api = LineBotApi(os.environ['LINE_CHANNEL_ACCESS_TOKEN'])
parser = WebhookParser(os.environ['LINE_CHANNEL_SECRET'])

#GPT-3のAPIキーを環境変数から取得する
openai.api_key = os.environ['OPENAI_API_KEY']

#line botのコードを実装
app = Flask(__name__)

@app.route("/callback", methods=['POST'])
def callback():
    signature = request.headers['X-Line-Signature']
    body = request.get_data(as_text=True)
    try:
        events = parser.parse(body, signature)
    except InvalidSignatureError:
        abort(400)

    for event in events:
        if isinstance(event, MessageEvent):
            if isinstance(event.message, TextMessage):
                text = event.message.text
                response = openai.Completion.create(
                  engine="text-davinci-002",
                  prompt=f"Input: {text}\nOutput:",
                  max_tokens=60,
                  n = 1,
                  stop=None,
                  temperature=0.5,
                )
                reply = response.choices[0].text.strip()
                line_bot_api.reply_message(
                    event.reply_token,
                    TextSendMessage(text=reply))
    return 'OK'

if __name__ == "__main__":
    app.run(debug=True)
