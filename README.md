# Airbnb Clone

1. Initialize the application using the command `eb init`. Choose the AWS region of your choice (Ireland in my case), the use "Python 3.6 running on 64bit Amazon Linux" and confirm that you don't want to setup SSH.
1. Create the environement using `eb create` and confirm all the defaults;
1. Create an RDS instance using "PostgreSQL 9.6.15-R1" as Version "Free tier" as Template;
    - When creation completes, under "Security group rules" select the DB's security group and edit the inbound rules. You have to configure the Elastic Beanstalk's Security Group as allowed.
    - Take in mind the endpoint, and password you choose for your DB
1. In Elastic Beanstalk open the environment you create, choose "Configuration" and in the row "Software" click "Edit". In the upcoming screen, under the "Environment properties" configure the following environment variables:
    – RDS_HOST=<the RDS endpoint>
    - RDS_USER=postgres
    - RDS_NAME=postgres
    - RDS_PASSWORD=<the password you choose>

1. Configure the migration in `02-django.config`, adding:

```txt
container_commands:
  01_migrate:
    command: "django-admin migrate"
    leader_only: true
```

1. Uncomment the `DATABASES` object (line 94) in settings.py so it will look like:

```txt
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": os.environ.get("RDS_HOST"),
        "NAME": os.environ.get("RDS_NAME"),
        "USER": os.environ.get("RDS_USER"),
        "PASSWORD": os.environ.get("RDS_PASSWORD"),
        "PORT": "5432",
    }
}
```

1. Execute `eb deploy`;
