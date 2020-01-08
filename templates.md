Django模板

===

 https://docs.djangoproject.com/en/1.11/ref/templates/builtins/ 

1. firstof
   输出不是的第一个参数变量false，如果所有传递的变量均为,则不输出任何内容false

    ```django
    {% firstof var1 var2 vvar3 %}
    等效于:
    {% if var1 %}
        {{ var1 }}
    {% elif var2 %}
        {{ var2 }}
    {% elif var3 %}
        {{ var3 }}
    {% endif %}
    ```

2. 反向遍历列表

   ```django
   <ul>
   {% for i in data reversed %}
   	<li>{{ i.id }}</li>
   {% endfor %}
   </ul>
   ```

3. 显示键值对

   ```django
   {% for key, value in data.items %}
   	{{ key }}: {{ value }}
   {% endfor %}
   ```

4. for 循环设置循环中可用的许多变量

   ```django
          变量                    描述
   forloop.counter       循环的当前迭代(1索引)
   forloop.counter0      循环的当前迭代(0索引)
   ```

   

