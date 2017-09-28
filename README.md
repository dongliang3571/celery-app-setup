# celery-app-setup

**Workflow**

![workflow](https://github.com/dongliang3571/celery-app-setup/blob/master/screenshots/workflow.png?raw=true "workflow")

**Install message broker**

For example, rabbitmq, redis and etc.

```
brew install rabbitmq
```

start rabbitmq service

```
brew services start rabbitmq
```

double check if it is running probably

```
brew services list
```

**Install result backend**

For example, cassandra, sql, nosql is recommended

```
brew install redis
```

**Install celery**

```
pip install celery
```

Create a Python file tasks.py with following code

```python
from celery import Celery

app = Celery('tasks', backend='redis://localhost/1', broker='amqp://localhost:5672')

CELERY_ROUTES = {
    'tasks.add': {
        'queue': 'math',
    }
}

app.conf['CELERY_ROUTES'] = CELERY_ROUTES

@app.task
def add(x, y):
    return x + y
```

Start worker

```
celery -A tasks worker -Q math --loglevel=info
```

Create a celery application to submit tasks

```python
import tasks

task.add.delay(1, 2)

# or

task.add.apply_aync([1, 2])
```

1. Messages are sent to exchanges.
2. An **exchange** routes messages to one or more queues.
3. he message waits in the queue until someone consumes it.
4. The message is deleted from the queue when it has been acknowledged.

1. Create an exchange
2. Create a queue
3. Bind the queue to the exchange.
