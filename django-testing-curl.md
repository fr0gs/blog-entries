# Making POST requests work with Django tests.


It is the second time at work that I spent some minutes wondering why I was not properly receiving *POST* arguments in a view when testing it from django. 

Let's have a little more context, imagine a very simple Django view where you want to print the value of a *POST* parameter in the console and return it.

```python
class MyView(views.APIView):
    def post(self, request):
        param = request.POST.get('param', None)
		print(param)
		return param
```


So I manually test this with curl:

```sh
curl -X POST -d 'param=fiesta' 'https://my.local.url/myview/'
```

It works, so now I just want to write a simple test using django test framework:

```python
def test_my_view(self):
	data = {
		'param': 'fiesta'
	}
	response = self.client.post(reverse('my-view), data)
	assertEqual(response, 'fiesta')	
```


However, the assert fails and the `print(param)` line always yields `None`, while when I was testing it with curl the parameter was always properly received. How come is that?


#### Explanation

Turns out when you send data in curl without specifying the **Content-Type** header, by default it sends the data in the `application/x-www-form-urlencoded` format. If you want to send json data in your request you have to set the `-H "Content-Type: application/json"` header properly. 

This means that the view is accessing the POST object of data only received in the `application/x-www-form-urlencoded` format, but the django test client, while calling the post in the line:

```python
response = self.client.post(reverse('my-view), data)
```

Is sending the data by default in json format, and thus the view is not able to access it.


#### Solution

There are two ways to overcome this:

1. Changing the `content_type` parameter in all tests to send `application/x-www-form-urlencoded` data, like: 

```python
response = self.client.post(reverse('my-view), data, content_type='application/x-www-form-urlencoded')
```

2. Accessing the raw data or the request instead of the form data of the POST object.

```python
param = request.data.get('param', None)
```



Have fun!



   
