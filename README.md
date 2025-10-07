# Ex.05 Design a Website for Server Side Processing
## Date: 08-10-2025

## AIM:
 To design a website to calculate the power of a lamp filament in an incandescent bulb in the server side. 


## FORMULA:
P = I<sup>2</sup>R
<br> P --> Power (in watts)
<br> I --> Intensity
<br> R --> Resistance

## DESIGN STEPS:

### Step 1:
Clone the repository from GitHub.

### Step 2:
Create Django Admin project.

### Step 3:
Create a New App under the Django Admin project.

### Step 4:
Create python programs for views and urls to perform server side processing.

### Step 5:
Create a HTML file to implement form based input and output.

### Step 6:
Publish the website in the given URL.

## PROGRAM :
```
mathapp.html

<!-- mathapp/templates/mathapp/math.html -->
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Lamp Filament Power Calculator</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    label { display:block; margin-top:10px; }
    input[type="number"] { padding:6px; width:200px; }
    .error { color: red; margin-top: 12px; }
    .result { color: green; margin-top: 12px; font-weight: bold; }
  </style>
</head>
<body>
  <h2>Power of Lamp Filament</h2>

  <form method="post" novalidate>
    {% csrf_token %}

    <label>Voltage (V):</label>
    <input type="number" name="voltage" step="any" value="{{ v }}">

    <label>Current (I) (A):</label>
    <input type="number" name="current" step="any" value="{{ i }}">

    <label>Resistance (R) (Ω):</label>
    <input type="number" name="resistance" step="any" value="{{ r }}">

    <div style="margin-top:12px;">
      <input type="submit" value="Calculate">
    </div>
  </form>

  {% if error %}
    <p class="error"> {{ error }}</p>
  {% endif %}

  {% if power != "" and not error %}
    <p class="result">Power = {{ power }} W</p>
  {% endif %}
</body>
</html>

views.py

# mathapp/views.py
from django.shortcuts import render

def powercalc(request):
    context = {
        'power': '',
        'v': '',
        'i': '',
        'r': '',
        'error': ''
    }

    if request.method == "POST":
        # Read and normalize inputs
        v = (request.POST.get('voltage', '') or '').strip()
        i = (request.POST.get('current', '') or '').strip()
        r = (request.POST.get('resistance', '') or '').strip()

        # Replace comma with dot if user typed "230,0"
        v = v.replace(',', '.') if v else v
        i = i.replace(',', '.') if i else i
        r = r.replace(',', '.') if r else r

        context['v'], context['i'], context['r'] = v, i, r

        # Try converting inputs to floats when provided
        try:
            V = float(v) if v != '' else None
            I = float(i) if i != '' else None
            R = float(r) if r != '' else None
        except ValueError:
            context['error'] = "Please enter valid numeric values (use digits, e.g. 230 or 0.26)."
            return render(request, "mathapp/mathapp.html", context)

        # Require at least two values
        provided_count = sum(x is not None for x in (V, I, R))
        if provided_count < 2:
            context['error'] = "Please provide at least two values (Voltage, Current or Resistance)."
            return render(request, "mathapp/mathapp.html", context)

        # Compute power by available formulas (prefer V*I if available)
        try:
            if V is not None and I is not None:
                power = V * I
            elif I is not None and R is not None:
                power = I * I * R
            elif V is not None and R is not None:
                if R == 0:
                    context['error'] = "Resistance cannot be zero."
                    return render(request, "mathapp/mathapp.html", context)
                power = (V * V) / R
            else:
                context['error'] = "Unexpected input combination."
                return render(request, "mathapp/mathapp.html", context)

            # Round result for display
            context['power'] = round(power, 4)
        except Exception as e:
            context['error'] = f"Computation error: {e}"

    return render(request, "mathapp/mathapp.html", context)

urls.py

# project/urls.py (or your project's urls file)
from django.contrib import admin
from django.urls import path
from mathapp import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('powercalc/', views.powercalc, name="powercalculator"),
    path('', views.powercalc, name="powercalcroot"),
]
```

## SERVER SIDE PROCESSING:
![server side ](https://github.com/user-attachments/assets/997affe3-f7ae-463e-baf0-4c1ae1461363)


## HOMEPAGE:
![homepage](https://github.com/user-attachments/assets/21ebe1d7-d56c-4674-863b-4c7fd230afd0)



## RESULT:
The program for performing server side processing is completed successfully.
