sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl status postgresql
sudo systemctl start postgresql

sudo -u postgres psql



CREATE DATABASE flowforge_db;

CREATE USER flowforge_user WITH PASSWORD 'password';

ALTER ROLE flowforge_user SET client_encoding TO 'utf8';
ALTER ROLE flowforge_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE flowforge_user SET timezone TO 'UTC';

GRANT ALL PRIVILEGES ON DATABASE flowforge_db TO flowforge_user;

\q


3. Install the PostgreSQL Python driver

because Python cannot communicate with a PostgreSQL database natively

You're already inside your virtual environment:

(.venv) developer@developer:~/.../FlowForge/backend$

So run:

pip install psycopg[binary]

Then verify:

pip show psycopg



use environment variables

> pip install python-dotenv