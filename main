from flask import Flask, request, jsonify
import yt_dlp
import os

app = Flask(__name__)

API_KEY = os.getenv("API_KEY", "kushanbob")  # fallback for local testing

@app.route('/')
def home():
    return '✅ yt-dlp API is online! Use /api?url=...'

@app.route('/api', methods=['GET'])
def get_video_info():
    key = request.headers.get('X-API-KEY')
    if key != API_KEY:
        return jsonify({'error': 'Unauthorized'}), 401

    url = request.args.get('url')
    if not url:
        return jsonify({'error': 'URL parameter is required'}), 400

    ydl_opts = {
        'quiet': True,
        'skip_download': True,
        'no_warnings': True,
    }

    try:
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=False)
            formats = info.get('formats', [])
            results = [
                {
                    'format_id': f['format_id'],
                    'ext': f['ext'],
                    'resolution': f.get('resolution') or f.get('height'),
                    'filesize': f.get('filesize'),
                    'url': f['url']
                } for f in formats if f.get('url')
            ]
            return jsonify({
                'title': info.get('title'),
                'thumbnail': info.get('thumbnail'),
                'duration': info.get('duration'),
                'formats': results
            })
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
