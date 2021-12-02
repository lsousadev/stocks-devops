## ABOUT THE APP

App to compare stock performance based on the day of the week for any timeframe. It offers four windows to choose a stock ticker, start date, and end date. Once you hit retrieve, it generates a table with performance data based on the day of the week, along with the option to show/hide price data for every day of the timeframe.

![App main page screenshot](./app/static/screenshot1.png)

## ABOUT THE PROJECT

This is a personal DevOps project. The primary goal is to learn and practice DevOps principles and tools. The secondary goal is to learn and practice cloud and security ops. Below is a loose draft of the progression path for this project.

- [x] Make a simple app (Flask + JS)
- [x] Containerize app (Docker)
- [x] Create very basic CI/CD process (Jenkins or Circle CI)
- [ ] Add basic tests to the CI
- [ ] Implement public URL to the deployment
- [ ] Use K8s
- [ ] Use Vault
- [ ] Deploy CI/CD via code (Terraform)
- [ ] Make a more complex new app (BME sensor) and redo above steps
- [ ] Monitor app (Datadog)
- [ ] Apollo for docs

## TO DO

- HTML/CSS/JS: clean up shenanigans
- JS: secondary table "date" column shows hours (00:00:00 GMT)
- python: handle overnight % for first day (prev. day close data)
- update app screenshot
- change docs from past tense to present imperative (from log to instructions)
- jenkinsfile: login on docker for docker push (Vault???), then update app version and start using feature branches
- ngrok config file for auto 4 tunnels (jenkins, ???, etc) https://ngrok.com/docs (basic "Implement public URL")
- try to create diagrams for docs

## CHANGELOG

**v0.0.4** *11/28/2021*
- Changed directory architecture
- Minor improvements

**v0.0.3**
- Created dockerfile and made app container-friendly

**v0.0.2**
- Fixed nav button path on index page

**v0.0.1**
- Very rudimentary working version of "Historical" app

## LINKS

### Making the app

- https://github.com/luk020/stockmarket/tree/master/historical
- https://github.com/luk020/cs50-finance
- https://github.com/luk020/stocks-devops
- https://pypi.org/project/yfinance/

### Project link ideas

- stocks-devops.lukenascimento.com
- jenkins.lukenascimento.com
- monitoring.lukenascimento.com
- apollo.lukenascimento.com