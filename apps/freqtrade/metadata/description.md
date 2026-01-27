# Overview
Freqtrade is a free and open source crypto trading bot written in Python. It is designed to support all major exchanges and be controlled via Telegram or webUI.

## Important Notes
- **Configuration Required**: You need to create a `config.json` file in the user_data directory with your exchange API keys and trading strategy settings
- **Default Strategy**: The container starts with `SampleStrategy` - you should replace this with your own strategy
- **API Access**: The REST API is exposed on port 9001 (mapped from container port 8080)
- **Data Persistence**: All user data including logs, database, and configuration is stored in `${APP_DATA_DIR}/freqtrade/user_data`

## Getting Started
1. After installation, you need to create a configuration file at `${APP_DATA_DIR}/freqtrade/user_data/config.json`
2. Add your exchange API keys and configure your trading parameters
3. Create or download a trading strategy and place it in `${APP_DATA_DIR}/freqtrade/user_data/strategies/`
4. Update the `--strategy` parameter in the docker-compose file to use your strategy
5. Restart the container

## Configurable Options
You can modify the docker-compose.yml to:
- Use different image tags (`develop`, `develop_plot`)
- Enable GPU support for freqAI (requires NVIDIA GPU)
- Change the command parameters for different trading modes (dry-run, backtesting, etc.)

## Links:
- [Official Documentation](https://www.freqtrade.io/en/stable/)
- [GitHub Repository](https://github.com/freqtrade/freqtrade)
- [REST API Documentation](https://www.freqtrade.io/en/stable/rest-api/)
- [Strategy Development](https://www.freqtrade.io/en/stable/strategy-customization/)

## Preview
![Freqtrade UI](https://www.freqtrade.io/en/stable/assets/freqtrade-ui.png)
