FROM python:3.12

WORKDIR /app

COPY requirements.txt .
COPY . .

RUN pip install -r requirements.txt

CMD ["python", "manage.py", "runserver", "--noreload", "0.0.0.0:8000"]