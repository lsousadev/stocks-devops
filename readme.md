## ABOUT THE APP
App to compare stock performance based on the day of the week for any timeframe. It offers four windows to choose a stock ticker, start date, and end date. Once you hit retrieve, it generates a table with performance data based on the day of the week, along with the option to show/hide price data for every day of the timeframe.

## ABOUT THE PROJECT
This is a personal DevOps project. The primary goal is to learn and practice DevOps principles and tools. The secondary goal is to learn and practice cloud and security ops. Below is a loose draft of the progression path for this project.

- [x] Make a simple app (Flask + JS)
- [x] Containerize app (Docker)
- [ ] Deploy via code (Terraform)
- [ ] Create very basic CI/CD process (Jenkins or Circle CI)
- [ ] Add basic tests to CI process
- [ ] Implement public URL to the deployment
- [ ] Use K8s
- [ ] Use Vault
- [ ] Make app more complex (BME sensor) and make ensure everything above works
- [ ] Monitor app (Datadog)

## TO DO
- upload docker image to dockerhub
- screenshot to readme.md
- clean up HTML/CSS/JS shenanigans

## CHANGELOG
**v0.0.3**
- Created dockerfile and made app container-friendly

**v0.0.2**
- Fixed nav button path on index page

**v0.0.1**
- Very rudimentary working version of "Historical" app
    - CSS: optimize
    - JS: fix a couple makeshift solutions, secondary table dates show hours (00:00:00 GMT)
    - Python: the first day of every request doesn't have a start price

## LINKS
### Making the app
- https://github.com/luk020/stockmarket/tree/master/historical
- https://github.com/luk020/cs50-finance
- https://github.com/luk020/watchlists/tree/master/watchlists
- https://pypi.org/project/yfinance/
### Containerizing the app
- https://runnable.com/docker/python/dockerize-your-flask-application
- https://matduggan.com/are-dockerfiles-good-enough/
- https://github.com/hadolint/hadolint