footer: Wei Tao
slidenumbers: true

# An intro to Pyspider
### A Powerful Spider(Web Crawler) System in Python.

---

# Features

- Write script in Python
- Powerful WebUI with script editor, task monitor, project manager and result viewer
- MySQL, MongoDB, Redis, SQLite, Elasticsearch; PostgreSQL with SQLAlchemy as database backend
- RabbitMQ, Beanstalk, Redis and Kombu as message queue
- Task priority, retry, periodical, recrawl by age, etc...
- Distributed architecture, Crawl Javascript pages, Python 2&3, etc...

---

# Installation

1. `pip install pyspider`
2. `pyspider -all`
3. visit http://localhost:5000/

---

# Components
- **Scheduler**: receive tasks from newtask_queue from processor
- **Fetcher**: fetch web pages and send results to processor
- **Processor**: run the script to parse and extract information
- **Result Worker** (optional): receive results from processor
- **WebUI**: a web frontend for everything

![right](/Users/wtao/Desktop/pyspider-arch.png)

---

# Demo

---

# References

- <http://docs.pyspider.org/en/latest/>
- <https://github.com/binux/pyspider>
