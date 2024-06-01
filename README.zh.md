# Synology-RClone
使用 RClone 備份 Synology

此 Template 不限於執行在 Synology NAS 上，只要有 Docker 的 NAS 皆可使用 

# 設定上傳任務
每一個 services 都是一個獨立的上傳任務，如何將任務定時執行這邊不加以闡述，請自行研究

使用的 Image 為 RClone 官方 Image

RClone 詳細的設定請參考 [RClone Configure](https://rclone.org/docs/#configure)
```yaml
services:
  template:
    image: rclone/rclone:${RCLONE_VERSION:-latest}
    environment:
      - PHP_TZ=${TZ:-Etc/GMT}
      - TZ=${TZ:-Etc/UTC}
      - PUID=${PUID:-1000}
      - PGID=${PGID:-1000}
      # 指定 rclone 的 config 資料夾
      - RCLONE_CONFIG_DIR=/rclone/config
      # 指定 rclone 的 config 檔案
      - RCLONE_CONFIG=/rclone/config/rclone.conf
      # 指定 rclone 的 log 檔案
      - RCLONE_LOG_FILE=/rclone/logs/template.log
      # 指定 rclone 的 temp 資料夾
      - RCLONE_TEMP_DIR=/rclone/tmp
      # 是否顯示進度
      - RCLONE_PROGRESS=true
      # 防止遍歷目錄樹，適用於目標目錄很大的情況。
      - RCLONE_NO_TRAVERSE=true
      # 刪除目標目錄中源目錄中沒有的檔案
      - RCLONE_DELETE_EXCLUDED=true
      # 指定要排除的檔案名稱
      - RCLONE_EXCLUDE_FROM=/rclone/.rcloneignore
      # 同時進行 8 個檔案的檢查
      - RCLONE_TRANSFERS=8
      # 同時進行 8 個檔案的檢查
      - RCLONE_CHECKERS=8
      # 啟用多執行緒流處理，每個檔案8個執行緒
      - RCLONE_MULTI_THREAD_STREAMS=8
      # 輸出訊息，1 代表 -v，2 代表 -vv
      - RCLONE_VERBOSE=1
      # 只複製過去 24 小時內修改過的檔案
      - RCLONE_MAX_AGE=24h
    working_dir: /rclone
    volumes:
      # 掛載 config 檔案至 container 中，僅讀
      - ./config/nas.conf:/rclone/config/rclone.conf:ro
      # 掛載 ignore 檔案至 container 中
      - ./.rcloneignore:/rclone/.rcloneignore
      # 掛載 log 目錄至 container 中
      - ./logs:/rclone/logs:rw
      # 掛載 temp 目錄至 container 中
      - ./tmp:/rclone/tmp:rw
      # 掛載要上傳的目錄至 container 中，僅讀
      - /foo/bar:/rclone/backup/bar:ro
    command: copy /rclone/backup/bar {remote}:/remote/path
    deploy:
      resources:
        limits:
          memory: 1024M

```