# Proyecto de seguimiento de gastos

## El registro de entrada y salida de dinero se puede mantener fácilmente con la ayuda del rastreador de gastos.  

Empecemos a desarrollar el proyecto...

<img style="display: block;-webkit-user-select: none;margin: auto;background-color: hsl(0, 0%, 90%);" src="https://user-images.githubusercontent.com/74038190/235224431-e8c8c12e-6826-47f1-89fb-2ddad83b3abf.gif" width="426" height="426">

Crearemos un rastreador de gastos que tomará detalles de nuestros gastos. 

Al completar el formulario de registro, la persona también deberán completar los detalles sobre los ingresos y la cantidad que desea ahorrar. 
Algunas personas ganan a diario, por lo que sus ingresos también se pueden agregar de forma regular. 
Los detalles de los gastos se mostrarán en forma de gráfico circular de forma semanal, mensual y anual. La instalación de django es imprescindible para comenzar con el proyecto Expense Tracker.

## Estructura de archivos:

1. Instale el marco django
2. Crear un proyecto y una aplicación
3. Models.py
4. Admin.py
5. Urls.py
6. Views.py

## 1. Instale el framework django:
Escriba el siguiente comando en cmd o en la ventana del terminal.

```bash
Pip install django
```

## 2. Cree un proyecto y una aplicación:
Cree un nuevo proyecto llamado ExpenseTracker y una aplicación para iniciar el proyecto. 
Escriba el siguiente comando en la ventana del terminal.

```bash
django-admin startproject ExpenseTracker
python mange.py startapp home
```

Cree una plantilla y una carpeta estática para almacenar tus archivos. 
La carpeta de plantillas contendrá todos los archivos html.
La carpeta estática contendrá todos los archivos css, imágenes y archivos javascript.

## 3. Models.py
La conectividad de la base de datos se realiza con la ayuda de models.py. 

Cree los siguientes modelos en models.py archivo en la aplicación de su proyecto.

#### Código de Ejemplo

Aquí está un fragmento de código Python que define algunos modelos de Django:

```python
from django.db import models
from django.utils.timezone import now
from django.contrib.auth.models import User
from django.conf import settings
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.db.models import Sum

# Opciones de categoría para gastos
SELECT_CATEGORY_CHOICES = [
    ("Food", "Food"),
    ("Travel", "Travel"),
    ("Shopping", "Shopping"),
    ("Necessities", "Necessities"),
    ("Entertainment", "Entertainment"),
    ("Other", "Other")
]

# Opciones para el tipo de adición de dinero
ADD_EXPENSE_CHOICES = [
    ("Expense", "Expense"),
    ("Income", "Income")
]

# Opciones para la profesión
PROFESSION_CHOICES = [
    ("Employee", "Employee"),
    ("Business", "Business"),
    ("Student", "Student"),
    ("Other", "Other")
]

class AddMoneyInfo(models.Model):
    user = models.ForeignKey(User, default=1, on_delete=models.CASCADE)
    add_money = models.CharField(max_length=10, choices=ADD_EXPENSE_CHOICES)
    quantity = models.BigIntegerField()
    date = models.DateField(default=now)
    category = models.CharField(max_length=20, choices=SELECT_CATEGORY_CHOICES, default='Food')

    class Meta:
        db_table = 'add_money_info'

class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    profession = models.CharField(max_length=10, choices=PROFESSION_CHOICES)
    savings = models.IntegerField(null=True, blank=True)
    income = models.BigIntegerField(null=True, blank=True)
    image = models.ImageField(upload_to='profile_image', blank=True)

    def __str__(self):
        return self.user.username
```


##### Explicación del código:

SELECT_CATEGORY_CHOICES , EXPENSE_CHOICES PROFESSION_CHOICES contienen la lista de opciones que se darán al completar el formulario de gastos.

a. Clave foránea: Establece una relación de muchos a uno.

b. Charfield(): Almacena cadenas de tamaño pequeño y grande en la base de datos.

c. BigIntegerField():Puede almacenar números de -9223372036854775808 a 9223372036854775807 en la base de datos.

d. Datefield(): Acepta la fecha como entrada.

e. Integerfield():Almacena números enteros en una base de datos.

f. Imagefield():Almacena imágenes en la base de datos.

## 4. Admin.py
Ayudará a registrar las tablas en la base de datos.

Registra tus modelos aquí..

```python
from django.contrib import admin
from .models import Addmoney_info, UserProfile
from django.contrib.sessions.models import Session

class Addmoney_infoAdmin(admin.ModelAdmin):
    list_display = ("user", "quantity", "Date", "Category", "add_money")

admin.site.register(Addmoney_info, Addmoney_infoAdmin)
admin.site.register(UserProfile)
admin.site.register(Session)
```

##### Explicación del código:

Addmoney_info, UserProfile son los nombres de los modelos que queremos registrar en la base de datos. 

list_display contiene el nombre de las columnas que se mostrarán en la base de datos.

Para almacenar estos modelos en la base de datos, ejecute el siguiente comando:

```bash
python manage.py makemigrations
python manage.py migrate
```

Para acceder a la base de datos, cree el superusuario. 
Ejecute el siguiente comando en la ventana de su terminal.

```bash
Python manage.py createsuperuser
```

## 5. Urls.py

```python
from django.contrib import admin
from django.urls import path, include
from . import views
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('', views.home, name='home'),
    path('index/', views.index, name='index'),
    path('register/', views.register, name='register'),
    path('handleSignup/', views.handleSignup, name='handleSignup'),
    path('handlelogin/', views.handlelogin, name='handlelogin'),
    path('handleLogout/', views.handleLogout, name='handleLogout'),

    path('reset_password/', 
         auth_views.PasswordResetView.as_view(template_name="home/reset_password.html"),
         name='reset_password'),
    path('reset_password_sent/', 
         auth_views.PasswordResetDoneView.as_view(template_name="home/reset_password_sent.html"),
         name='password_reset_done'),
    path('reset/<uidb64>/<token>/', 
         auth_views.PasswordResetConfirmView.as_view(template_name="home/password_reset_form.html"),
         name='password_reset_confirm'),
    path('reset_password_complete/', 
         auth_views.PasswordResetCompleteView.as_view(template_name="home/password_reset_done.html"),
         name='password_reset_complete'),

    path('addmoney/', views.addmoney, name='addmoney'),
    path('addmoney_submission/', views.addmoney_submission, name='addmoney_submission'),
    path('charts/', views.charts, name='charts'),
    path('tables/', views.tables, name='tables'),
    path('expense_edit/<int:id>/', views.expense_edit, name='expense_edit'),
    path('<int:id>/addmoney_update/', views.addmoney_update, name='addmoney_update'),
    path('expense_delete/<int:id>/', views.expense_delete, name='expense_delete'),
    path('profile/', views.profile, name='profile'),
    path('expense_month/', views.expense_month, name='expense_month'),
    path('stats/', views.stats, name='stats'),
    path('expense_week/', views.expense_week, name='expense_week'),
    path('weekly/', views.weekly, name='weekly'),
    path('check/', views.check, name='check'),
    path('search/', views.search, name='search'),
    path('<int:id>/profile_edit/', views.profile_edit, name='profile_edit'),
    path('<int:id>/profile_update/', views.profile_update, name='profile_update'),
    path('info/', views.info, name='info'),
    path('info_year/', views.info_year, name='info_year'),
]
```

##### Explicación del código:

### Estos son los nombres de las urls a las que podemos acceder.

##### Si intentamos acceder a urls distintas a estas, nos dará un error.

a. path(): Se utiliza para enrutar la url con las vistas de funciones en la carpeta de tu aplicación.

b. include(): Un elemento es devuelto por él, para incluir ese elemento en urlpatterns.

## 6. Views.py

### a. Importación de módulos

```python
from django.shortcuts import render, HttpResponse, redirect
from django.contrib import messages
from django.contrib.auth import authenticate, logout, login as dj_login
from django.contrib.auth.models import User
from .models import Addmoney_info, UserProfile
from django.contrib.sessions.models import Session
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.db.models import Sum
from django.http import JsonResponse
from django.utils import timezone
import datetime
```

##### Explicación del código:

a. render: Devuelve el objeto Httpresponse y combina la plantilla con el diccionario que se menciona en ella.

b. HttpResponse: Muestra una respuesta de texto al usuario.

c. Redirect: Redirige al usuario a la url especificada.

d. messages: Ayuda a almacenar y mostrar mensajes al usuario en la pantalla.

e. authenticate: Verifica al usuario.

f. user: Este modelo se encarga de la autenticación y la autorización.

g. session: Ayuda al usuario a acceder únicamente a sus datos. Sin sesiones, los datos de cada usuario se mostrarán al usuario.

h. paginator: Se utiliza para gestionar datos paginados.

i. datetime: Se utiliza para obtener la fecha y hora actuales.

### b. Función de inicio de sesión e índice

```python
def home(request):
    if 'is_logged' in request.session:
        return redirect('/index')
    return render(request, 'home/login.html')

def index(request):
    if 'is_logged' in request.session:
        user_id = request.session.get("user_id")
        user = User.objects.get(id=user_id)
        addmoney_info = Addmoney_info.objects.filter(user=user).order_by('-Date')
        paginator = Paginator(addmoney_info, 4)
        page_number = request.GET.get('page')
        page_obj = paginator.get_page(page_number)
        context = {
            'add_info': addmoney_info,
            'page_obj': page_obj
        }
        return render(request, 'home/index.html', context)
    return redirect('home')
```

##### Explicación del código:

home() es una función que permite al usuario acceder al panel una vez que el usuario ha iniciado sesión. 

index() contiene el backend de la página del panel de control.

a. filter(): El conjunto de consultas es filtrado por filter().

b. get(): Se puede obtener un solo objeto único con get().

c. order_by(): Ordena el conjunto de consultas.

### c. Otras funciones

```python
def addmoney(request):
    return render(request, 'home/addmoney.html')

def profile(request):
    if 'is_logged' in request.session:
        return render(request, 'home/profile.html')
    return redirect('/home')

def profile_edit(request, id):
    if 'is_logged' in request.session:
        add = User.objects.get(id=id)
        return render(request, 'home/profile_edit.html', {'add': add})
    return redirect('/home')
```

##### Explicación del código:

La primera función redirige al usuario a la página donde podemos introducir nuestros gastos e ingresos.

profile() redirige al usuario a la página de perfil donde se muestra la información del usuario. 

profile_edit() redirige a la página donde se puede editar la información del usuario. 

Solo se puede acceder a estas páginas si el usuario ha iniciado sesión.

d. Actualización del perfil

```python
def profile_update(request, id):
    if 'is_logged' in request.session:
        if request.method == "POST":
            user = User.objects.get(id=id)
            user.first_name = request.POST.get("fname")
            user.last_name = request.POST.get("lname")
            user.email = request.POST.get("email")

            # Ensure that userprofile exists and update fields
            if hasattr(user, 'userprofile'):
                user.userprofile.Savings = request.POST.get("Savings")
                user.userprofile.income = request.POST.get("income")
                user.userprofile.profession = request.POST.get("profession")
                user.userprofile.save()

            user.save()
            return redirect("/profile")
    return redirect("/home")
```   

##### Explicación del código:

profile_update() realiza el backend del formulario de edición de perfil. 

User.objects.get() obtiene toda la información del usuario y luego toda la información actualizada se guarda de nuevo.

Esta función la realiza save().

e. Backend de registro, inicio de sesión y cierre de sesión:

```python
def handleSignup(request):
    if request.method == 'POST':
        # Get the post parameters
        uname = request.POST.get("uname")
        fname = request.POST.get("fname")
        lname = request.POST.get("lname")
        email = request.POST.get("email")
        profession = request.POST.get('profession')
        savings = request.POST.get('Savings')
        income = request.POST.get('income')
        pass1 = request.POST.get("pass1")
        pass2 = request.POST.get("pass2")
        
        # Check for errors in input
        try:
            User.objects.get(username=uname)
            messages.error(request, "Username already taken, Try something else!!!")
            return redirect("/register")
        except User.DoesNotExist:
            if len(uname) > 15:
                messages.error(request, "Username must be max 15 characters, Please try again")
                return redirect("/register")

            if not uname.isalnum():
                messages.error(request, "Username should only contain letters and numbers, Please try again")
                return redirect("/register")

            if pass1 != pass2:
                messages.error(request, "Password does not match, Please try again")
                return redirect("/register")

            # Create the user
            user = User.objects.create_user(username=uname, email=email, password=pass1)
            user.first_name = fname
            user.last_name = lname
            user.email = email
            
            # Save the user
            user.save()
            
            # Create and save user profile
            profile = UserProfile(Savings=savings, profession=profession, income=income, user=user)
            profile.save()
            
            messages.success(request, "Your account has been successfully created")
            return redirect("/")
    else:
        return HttpResponse('404 - NOT FOUND')
    
def handlelogin(request):
    if request.method == 'POST':
        # Get the post parameters
        loginuname = request.POST.get("loginuname")
        loginpassword1 = request.POST.get("loginpassword1")
        
        user = authenticate(username=loginuname, password=loginpassword1)
        if user is not None:
            dj_login(request, user)
            request.session['is_logged'] = True
            request.session["user_id"] = user.id
            messages.success(request, "Successfully logged in")
            return redirect('/index')
        else:
            messages.error(request, "Invalid credentials, Please try again")
            return redirect("/")
    
    return HttpResponse('404 - NOT FOUND')

def handleLogout(request):
    if 'is_logged' in request.session:
        del request.session['is_logged']
        del request.session["user_id"]
    
    logout(request)
    messages.success(request, "Successfully logged out")
    return redirect('home')
```


##### Explicación del código:

handlesignup() maneja el backend del formulario de registro. Uname, fname, lname, email, pass1, pass2, income, savings y profession

almacenarán la información del formulario en estas variables.

Hay varias condiciones para inscribirse...  
El nombre de usuario debe ser único, pass1 y pass 2 deben ser iguales y también la longitud  
del nombre de usuario debe tener un máximo de 15 caracteres. 

handlelogin() maneja el backend de la página de inicio de sesión. Si la información introducida por el usuario es correcta, el usuario

será redirigido al panel de control. 

handleLogout() maneja el backend de logout.

a. error(): Esta función da el mensaje de error en la pantalla si una condición no se cumple.

b. len():Esta función devuelve la longitud de la cadena, matriz, diccionario, etc.

c. success(): Si se cumple una condición, muestra el mensaje que se especifica entre paréntesis.

### f. Agregar formulario de dinero y agregar backend de actualización de dinero:

```python
def addmoney_submission(request):
    if 'is_logged' in request.session:
        if request.method == "POST":
            user_id = request.session.get("user_id")
            user = User.objects.get(id=user_id)
            addmoney_info_list = Addmoney_info.objects.filter(user=user).order_by('-Date')
            
            add_money = request.POST.get("add_money")
            quantity = request.POST.get("quantity")
            date = request.POST.get("Date")
            category = request.POST.get("Category")
            
            add = Addmoney_info(
                user=user,
                add_money=add_money,
                quantity=quantity,
                Date=date,
                Category=category
            )
            add.save()
            
            paginator = Paginator(addmoney_info_list, 4)
            page_number = request.GET.get('page')
            page_obj = paginator.get_page(page_number)
            
            context = {
                'page_obj': page_obj
            }
            return render(request, 'home/index.html', context)
    
    return redirect('/index')

def addmoney_update(request, id):
    if 'is_logged' in request.session:
        if request.method == "POST":
            add = Addmoney_info.objects.get(id=id)
            
            add.add_money = request.POST.get("add_money")
            add.quantity = request.POST.get("quantity")
            add.Date = request.POST.get("Date")
            add.Category = request.POST.get("Category")
            
            add.save()
            return redirect("/index")
    
    return redirect("/home")
```  

##### Explicación del código:

addmoney_submission() maneja el backend del formulario que completamos para nuestros gastos diarios.

addmoney_update() guarda la información del formulario después de que hayamos editado .

### g. Backend de edición de gastos y eliminación de gastos:

```python
def expense_edit(request, id):
    if 'is_logged' in request.session:
        addmoney_info = Addmoney_info.objects.get(id=id)
        return render(request, 'home/expense_edit.html', {'addmoney_info': addmoney_info})
    
    return redirect("/home")

def expense_delete(request, id):
    if 'is_logged' in request.session:
        addmoney_info = Addmoney_info.objects.get(id=id)
        addmoney_info.delete()
        return redirect("/index")
    
    return redirect("/home")
``` 

##### Explicación del código:

expense_edit() redirige al usuario al formulario de edición y también extrae los detalles del usuario de la base de datos y los

muestra en la pantalla. 

expense_delete() ayuda a eliminar los gastos.

### h. Backend de gastos mensuales, semanales y anuales  

```python
def expense_month(request):
    todays_date = datetime.date.today()
    one_month_ago = todays_date - datetime.timedelta(days=30)
    user_id = request.session["user_id"]
    user = User.objects.get(id=user_id)
    addmoney = Addmoney_info.objects.filter(user=user, Date__gte=one_month_ago, Date__lte=todays_date)
    final_rep = {}

    def get_category(addmoney_info):
        return addmoney_info.Category

    category_list = list(set(map(get_category, addmoney)))

    def get_expense_category_amount(category, add_money):
        quantity = 0
        filtered_by_category = addmoney.filter(Category=category, add_money="Expense")
        for item in filtered_by_category:
            quantity += item.quantity
        return quantity

    for category in category_list:
        final_rep[category] = get_expense_category_amount(category, "Expense")

    return JsonResponse({'expense_category_data': final_rep}, safe=False)

def stats(request):
    if 'is_logged' in request.session:
        todays_date = datetime.date.today()
        one_month_ago = todays_date - datetime.timedelta(days=30)
        user_id = request.session["user_id"]
        user = User.objects.get(id=user_id)
        addmoney_info = Addmoney_info.objects.filter(user=user, Date__gte=one_month_ago, Date__lte=todays_date)
        
        expense_sum = sum(i.quantity for i in addmoney_info if i.add_money == 'Expense')
        income_sum = sum(i.quantity for i in addmoney_info if i.add_money == 'Income')

        x = user.userprofile.Savings + income_sum - expense_sum
        y = x

        if x < 0:
            messages.warning(request, 'Your expenses exceeded your savings')
            x = 0
        if x > 0:
            y = 0

        return render(request, 'home/stats.html', {
            'expense_sum': expense_sum,
            'income_sum': income_sum,
            'x': abs(x),
            'y': abs(y)
        })

def expense_week(request):
    todays_date = datetime.date.today()
    one_week_ago = todays_date - datetime.timedelta(days=7)
    user_id = request.session["user_id"]
    user = User.objects.get(id=user_id)
    addmoney = Addmoney_info.objects.filter(user=user, Date__gte=one_week_ago, Date__lte=todays_date)
    final_rep = {}

    def get_category(addmoney_info):
        return addmoney_info.Category

    category_list = list(set(map(get_category, addmoney)))

    def get_expense_category_amount(category, add_money):
        quantity = 0
        filtered_by_category = addmoney.filter(Category=category, add_money="Expense")
        for item in filtered_by_category:
            quantity += item.quantity
        return quantity

    for category in category_list:
        final_rep[category] = get_expense_category_amount(category, "Expense")

    return JsonResponse({'expense_category_data': final_rep}, safe=False)

def weekly(request):
    if 'is_logged' in request.session:
        todays_date = datetime.date.today()
        one_week_ago = todays_date - datetime.timedelta(days=7)
        user_id = request.session["user_id"]
        user = User.objects.get(id=user_id)
        addmoney_info = Addmoney_info.objects.filter(user=user, Date__gte=one_week_ago, Date__lte=todays_date)
        
        expense_sum = sum(i.quantity for i in addmoney_info if i.add_money == 'Expense')
        income_sum = sum(i.quantity for i in addmoney_info if i.add_money == 'Income')

        x = user.userprofile.Savings + income_sum - expense_sum
        y = x

        if x < 0:
            messages.warning(request, 'Your expenses exceeded your savings')
            x = 0
        if x > 0:
            y = 0

        return render(request, 'home/weekly.html', {
            'expense_sum': expense_sum,
            'income_sum': income_sum,
            'x': abs(x),
            'y': abs(y)
        })

def check(request):
    if request.method == 'POST':
        if not User.objects.filter(email=request.POST['email']).exists():
            messages.error(request, "Email not registered, TRY AGAIN!!!")
        return redirect("/reset_password")

def info_year(request):
    todays_date = datetime.date.today()
    one_year_ago = todays_date - datetime.timedelta(days=365)
    user_id = request.session["user_id"]
    user = User.objects.get(id=user_id)
    addmoney = Addmoney_info.objects.filter(user=user, Date__gte=one_year_ago, Date__lte=todays_date)
    final_rep = {}

    def get_category(addmoney_info):
        return addmoney_info.Category

    category_list = list(set(map(get_category, addmoney)))

    def get_expense_category_amount(category, add_money):
        quantity = 0
        filtered_by_category = addmoney.filter(Category=category, add_money="Expense")
        for item in filtered_by_category:
            quantity += item.quantity
        return quantity

    for category in category_list:
        final_rep[category] = get_expense_category_amount(category, "Expense")

    return JsonResponse({'expense_category_data': final_rep}, safe=False)

def info(request):
    return render(request, 'home/info.html')
```

##### Explicación del código:

expense_month() obtiene los datos de los gastos del mes actual. 

La función get_category() obtiene la categoría (gasto/ingreso) de la base de datos.

get_expense_category_amount() obtiene el importe de la base de datos de la categoría (gasto).

La función stats() calcula los gastos generales y los ahorros realizados por el usuario en un mes.

expense_week() y info_year() realizan la misma función que expense_month() pero semanalmente.

weekly() obtiene la cantidad ahorrada en un mes y también los gastos generales de un usuario.

## Salida del rastreador de gastos de Python:

#### Formulario de inicio de sesión:

![image](https://github.com/user-attachments/assets/231fe8f7-d304-4823-8059-e72a7e6fa5e7)

#### Dashboard:

![image](https://github.com/user-attachments/assets/c7492554-91f7-4a01-8732-8bbc02b94916)

![image](https://github.com/user-attachments/assets/1fbdf19f-af5f-4593-bfd8-b73cacfeaef6)

#### Página de Gastos Mensuales:

![image](https://github.com/user-attachments/assets/9666bdc5-7ed9-43b7-aac4-c988146519f6)

Página de historial:

![image](https://github.com/user-attachments/assets/600c23f7-c8ac-462c-9502-f5cf48b36a65)


        
    
