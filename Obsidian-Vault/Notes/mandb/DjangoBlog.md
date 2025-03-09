#Activate Virtual Env
virtualenv venv
source venv/bin/activate
pip install django

django-admin startproject "Project-Name"
cd "Project-Name"
python3 manage.py startapp 'app-Name'
python3 manage.py startapp users <-- for authentication

ADD both apps to INSTALLED_APPS list in Settings.py

python3 manage.py migrate
python3 manage.py createsuperuser <== if not done earlier
python3 manage.py runserver


#Creating View inside an App

#Creating Urls inside an App and connecting to base urls
#Creating Template
# bootswatch --> bootstrap css
# bootstrap 5 navigation bar
# crispy-forms
#
#Creating Models
#
#tinymce
