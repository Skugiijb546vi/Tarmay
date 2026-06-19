import os
import subprocess
from huggingface_hub import HfApi

# --- ڕێکخستنەکان ---
# لینکەکە لە گیتهەب ئەکشنەوە وەردەگرێت
VIDEO_URL = os.environ.get("VIDEO_URL")
ORIGINAL_FILE = "movie_original.mp4"
COMPRESSED_FILE = "movie_compressed.mp4"
REPO_ID = "Sarko717227/Tarmayai" # داتابەیسەکەی خۆت
HF_TOKEN = os.environ.get("HF_TOKEN")

MAX_SIZE_BYTES = 1.9 * 1024 * 1024 * 1024  

def download_video():
    print(f"📥 خەریکی داگرتنی فیلمەکە لەم لینکەوە: {VIDEO_URL}")
    
    command = [
        "ffmpeg", 
        "-i", VIDEO_URL, 
        "-c", "copy", 
        ORIGINAL_FILE
    ]
    
    subprocess.run(command, check=True)
    print("✅ داگرتن تەواو بوو!")

def check_and_compress():
    file_size = os.path.getsize(ORIGINAL_FILE)
    size_in_gb = file_size / (1024 * 1024 * 1024)
    
    print(f"📊 قەبارەی فیلمەکە: {size_in_gb:.2f} GB")
    
    if file_size > MAX_SIZE_BYTES:
        print("✂️ قەبارەی فیلمەکە لە 1.9 گێگا زیاترە، دەست دەکەم بە بچووککردنەوەی...")
        command = [
            "ffmpeg", 
            "-i", ORIGINAL_FILE, 
            "-c:v", "libx265", 
            "-crf", "28", 
            "-preset", "fast", 
            "-c:a", "aac", 
            "-b:a", "128k", 
            COMPRESSED_FILE
        ]
        subprocess.run(command, check=True)
        print("✅ بچووککردنەوە تەواو بوو!")
        return COMPRESSED_FILE
    else:
        print("👍 قەبارەی فیلمەکە گونجاوە (لە خوار 1.9 گێگایە)، پێویست بە بچووککردنەوە ناکات.")
        return ORIGINAL_FILE

def upload_to_huggingface(file_to_upload):
    print("🚀 خەریکی ئەپڵۆدکردنی ڤیدیۆکە بۆ Hugging Face...")
    api = HfApi()
    
    api.upload_file(
        path_or_fileobj=file_to_upload,
        path_in_repo="movie.mp4",
        repo_id=REPO_ID,
        repo_type="dataset", 
        token=HF_TOKEN
    )
    print("🎉 ئەپڵۆدکردن بە سەرکەوتوویی کۆتایی هات!")

if __name__ == "__main__":
    if not HF_TOKEN:
        print("❌ هەڵە: تۆکنی Hugging Face (HF_TOKEN) نەدۆزرایەوە!")
        exit(1)
    if not VIDEO_URL:
        print("❌ هەڵە: لینکی فیلمەکە نەدراوە! تکایە لە گیتهەب ئەکشن لینکەکە دابنێ.")
        exit(1)
        
    download_video()
    final_video = check_and_compress()
    upload_to_huggingface(final_video)
