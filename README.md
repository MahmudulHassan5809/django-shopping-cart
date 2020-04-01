
Django Shopping Cart
------


Django Shopping Cart is a Django app to store product in cart.
======



Quick start
-----------

**1. **install**::**   ```pip install django-shopping-cart```

**2. Add "cart" to your INSTALLED_APPS setting like this::**

    ```
    INSTALLED_APPS = [
        ...
        'cart',
    ]
    CART_SESSION_ID = 'cart'
    ```

    **Add below line to settings context_processor::** 
    ```'cart.context_processor.cart_total_amount'```




**3. You can use the urls in following way::**

    from django.urls import path
    from . import views

    urlpatterns = [
        path('cart/add/<int:id>/', views.cart_add, name='cart_add'),
        path('cart/item_clear/<int:id>/', views.item_clear, name='item_clear'),
        path('cart/item_increment/<int:id>/',
             views.item_increment, name='item_increment'),
        path('cart/item_decrement/<int:id>/',
             views.item_decrement, name='item_decrement'),
        path('cart/cart_clear/', views.cart_clear, name='cart_clear'),
        path('cart/cart-detail/',views.cart_detail,name='cart_detail'),
    ]

**4. You should have a Product model & Below field is mandatory**
    ```

    name = models.CharField(max_length=255)
    image = models.ImageField(upload_to='products/')
    price = models.FloatField()
    ```


**5. Then views.py should like like this::**

    from django.shortcuts import render, redirect
    from store.models import Product
    from django.contrib.auth.decorators import login_required
    from cart.cart import Cart

    @login_required(login_url="/users/login")
    def cart_add(request, id):
        cart = Cart(request)
        product = Product.objects.get(id=id)
        cart.add(product=product)
        return redirect("home")


    @login_required(login_url="/users/login")
    def item_clear(request, id):
        cart = Cart(request)
        product = Product.objects.get(id=id)
        cart.remove(product)
        return redirect("cart_detail")


    @login_required(login_url="/users/login")
    def item_increment(request, id):
        cart = Cart(request)
        product = Product.objects.get(id=id)
        cart.add(product=product)
        return redirect("cart_detail")


    @login_required(login_url="/users/login")
    def item_decrement(request, id):
        cart = Cart(request)
        product = Product.objects.get(id=id)
        cart.decrement(product=product)
        return redirect("cart_detail")


    @login_required(login_url="/users/login")
    def cart_clear(request):
        cart = Cart(request)
        cart.clear()
        return redirect("cart_detail")


    @login_required(login_url="/users/login")
    def cart_detail(request):
        return render(request, 'cart/cart_detail.html')


**6. In the template you can use the url in folowing way::**

    <a href="{% url 'cart_add' product.id %}">Add To Cart</a>
    <a href="{% url 'cart_clear' %}">Clear Cart</a>
    <a href="{% url 'item_increment' value.product_id %}">Increament</a>
    <a href="{% url 'item_decrement' value.product_id %}">Decrement</a>

**7. To view the cart detail page use the below code**

    {% load cart_tag %}

    Total Length :: {{request.session.cart|length}}

    Cart Detail::
    {% for key,value in request.session.cart.items %}
        <img src="{{value.image}}" width="120" height="80"><br>
        {{value.name}}<br>
        {{value.price}} <br>
        {{value.quantity}}  <br>
        Total {{ value.price|multiply:value.quantity }}
    {% endfor %}



