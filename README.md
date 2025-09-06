# EURUSD-oanda-bot


## ✅ What this integrated bot does
-**Fetches candles (default H2) for EUR/USD (configurable).
-**Computes EMA(10,20,50) and VWAP
-**Executes your Step 1–4 trading logic.
-**Places market orders with configurable TP/SL and units.
-**Logs trades in logs/trades_log.csv.
-**Sends real-time Telegram alerts for every order.
-**Sends daily summary at UTC time configured.
-**Handles exceptions and auto-retries.
-**Fully configurable via config.yaml.


# 📈 OANDA Automated Trading Bot

An **end-to-end automated futures trading bot** for **EUR/USD (default)** that runs **24×5** on free infrastructure.  
The bot executes trades on **OANDA** using EMA and VWAP-based strategy logic with configurable parameters.  

---

## 🚀 Features

- **Instruments**  
  - Default: EUR/USD  
  - Configurable for any OANDA-supported instrument  

- **Indicators & Strategy**  
  - EMA (default: 10, 20, 50)  
  - VWAP (intraday/rolling)  
  - Configurable candle interval (default: 2H, supports M10, M30, H1, H4)  
  - Custom strategy rules:  
    - Long entry if price > VWAP and EMA(10) > EMA(20) > EMA(50)
    - Short entry is vice versa  
  - Trade size: default 1000 units (configurable)  
  - Take Profit (TP): 25 pips  
  - Stop Loss (SL): 100 pips  
  - Daily stop-loss cap: 100 pips  

- **Risk Management**  
  - Configurable pip-based TP and SL  
  - Daily loss cap  
  - Unlimited or max concurrent trades (configurable)  

- **Execution**  
  - Uses OANDA v20 REST API  
  - Trades placed automatically when conditions are met  

- **Logging**  
  - All trades logged in `logs/trades_log.csv`  
  - Daily summary log  

- **Alerts**  
  - Real-time trade alerts via Telegram  
  - Daily PnL summary via Telegram  

- **Hosting**  
  - Dockerized for portability  
  - Can run locally or 24×5 on Oracle Cloud Free VM  
  - Auto-update from GitHub daily  

---

## 🛠 Project Structure

```
oanda-bot/
│── bot.py                # Main trading loop
│── strategy.py           # Trading logic (EMA, VWAP rules)
│── indicators.py         # Indicator calculations
│── telegram_alerts.py    # Real-time + daily alerts
│── daily_summary.py      # Daily PnL summaries
│── config.yaml           # Configurable parameters
│── requirements.txt      # Python dependencies
│── Dockerfile            # Container build file
│── docker-compose.yml    # Docker service setup
│── full_deploy_oanda.sh  # Auto-deploy script
│── logs/
│    └── trades_log.csv   # Trade logs
```

---

## ⚙️ Configuration (`config.yaml`)

```yaml
oanda:
  env: practice
  token_env: OANDA_TOKEN
  account_env: OANDA_ACCOUNT_ID

instruments:
  - EUR_USD

data:
  timeframe: H2
  ema_periods: [10, 20, 50]
  vwap:
    mode: intraday
    window: 20

strategy:
  entry_gap_pips: 30
  tp_pips: 25
  sl_pips: 100
  units: 1000
  max_positions: null
  daily_loss_limit_pips: 100

alerts:
  telegram:
    enabled: true
    bot_token_env: TELEGRAM_BOT_TOKEN
    chat_id_env: TELEGRAM_CHAT_ID
    daily_summary_time: "23:59"
```

---

## 🖥 Running Locally

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/oanda-bot.git
cd oanda-bot
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Set environment variables

```bash
export OANDA_TOKEN="your_oanda_api_token"
export OANDA_ACCOUNT_ID="your_oanda_account_id"
export TELEGRAM_BOT_TOKEN="your_telegram_bot_token"
export TELEGRAM_CHAT_ID="your_telegram_chat_id"
```

### 4. Run the bot

```bash
python bot.py
```

---

## ☁️ Running on Oracle Cloud VM (Free Tier)

### 1. Launch VM
- Use **Always Free eligible VM** (Ubuntu 22.04 recommended).  
- Minimum: 1 vCPU, 1 GB RAM.  

### 2. Connect via SSH

```bash
ssh -i /path/to/key opc@<vm_public_ip>
```

### 3. Upload project

```bash
scp oanda-bot-deploy.zip opc@<vm_ip>:/home/opc/
```

### 4. Unpack

```bash
unzip oanda-bot-deploy.zip
cd oanda-bot-deploy
```

### 5. Make deploy script executable

```bash
chmod +x full_deploy_oanda.sh
```

### 6. Configure environment variables inside `full_deploy_oanda.sh`

Edit file and set:

```bash
OANDA_TOKEN="your_oanda_api_token"
OANDA_ACCOUNT_ID="your_account_id"
TELEGRAM_BOT_TOKEN="your_telegram_bot_token"
TELEGRAM_CHAT_ID="your_chat_id"
```

### 7. Deploy bot

```bash
./full_deploy_oanda.sh
```

This will:
- Install Docker if missing  
- Build and run container  
- Mount logs for persistence  
- Add daily auto-update cronjob  

### 8. Verify bot is running

```bash
docker ps -f name=oanda-bot
docker logs -f oanda-bot
```

---

## 📊 Monitoring

- **Logs:** `logs/trades_log.csv`  
- **Alerts:** Telegram notifications for every trade and daily summary  

---

## 🔄 Updating

Bot auto-updates daily via cron from GitHub.  
To trigger manually:

```bash
./full_deploy_oanda.sh
```

---
