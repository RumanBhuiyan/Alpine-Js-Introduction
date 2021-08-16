# Alpine-Js-Introduction
My Alpine Js Learning Notes which is the best alternative to jquery

> ## Accessing ALpine over CDN
```html
    <head>
        <script defer src="https://unpkg.com/alpinejs@3.x.x/dist/cdn.min.js"></script>
    </head>

```

> ## x-data
```html
       <div x-data="{
                    myAge : 24,
                    firstName : 'Ruman',
                    lastName : function () { return 'Bhuiyan'},
                    age() { return this.myAge},
                    incrementAge() { this.myAge++},
                    decrementAge() { this.myAge--},
                }">
            <p x-text="firstName"></p>   
            <p x-text="lastName"></p> <!--works also <p x-text="lastName()"></p>  -->
            <p x-text="age()"></p>
            <button x-on:click="incrementAge" >Age+</button>
            <!--works also <button x-on:click="incrementAge()" >Increase Age</button> -->
            <button @click="decrementAge" >Age-</button>
        </div>
```
Here x-data is creating a state/store which is a plain javascript object basically. Properties of the object will only be accessible to
this HTML elements children. we can create nested store/state also inside this element which have same scope of nested loop concept.
x-text is setting inner text of HTML element. click is an event and we can call all events of our DOM in this way 
x-on:mouseover or @mouseover . Here for executing a function by Alpine we can pass function reference or execute function directly like
 x-text="lastName"  or x-text="lastName()" . Both are understandable by Alpine.
Look at here , 

```html 
            <div x-data>
              <button @click="console.log('Hello World')">Click Me</button> 
            </div>
```
			
if you want to us other tags of alpine like x-text , x-show you must initialize x-data first otherwise they wont work.
you can initialize x-data ="{}" like also.

> ## x-init
```html
           <div x-data="{users :[]}" 
               x-init= "axios.get('https://www.testjsonapi.com/users/').then((res)=>{
                  users = res.data; 
               })">

               <template x-for="user in users">
                  <li x-text="user.name"></li>    
               </template>
            </div>
```
x-init is called when first time an element is rendered into dom so we can call an API to store some data at the time of
initialization so that we can use it inside its children. Here we could also use $nextTick() to wait untill data come from API and
after coming re-render the DOM.

```html
           x-init= "$nextTick(()=>{
                     axios.get('https://www.testjsonapi.com/users/').then((res)=>{
                     users = res.data; 
                 })
                 })" 
```
> ## x-show
```html
            <div x-data="{showMyName : false, name : 'Ruman Bhuiyan'}">

                <p x-show="showMyName" x-text="name"></p>
                 <button 
                 x-text="showMyName ? 'Hide Name' : 'Show Name' "
                 @click ="showMyName = !showMyName"
                 ></button>

            </div>
```
x-show controls the visibility of any HTML element . if x-show becomes true then element is visible otherwise not.
so to say x-show makes css display property none if x-show becomes false.

> ## x-bind
```html
             <div x-data = "{ changeColor: false }">
                  <p x-bind:class="changeColor && 'text-purple-700'"> Hello World</p>
                  <p :class="changeColor ?'text-red-700' : 'text-purple-700' ">
                       How are You</p>
                  <button @click="changeColor = !changeColor">Click Me</button> 
              </div>
```
x-bind allows you to set HTML attributes on elements based on the result of JavaScript expressions. 
we can bind other attributes like placeholder, href, name , value etc basis on javascript condition.

> ## x-html
```html
           <div x-data = "{ name : 'Ruman Bhuiyan' }">
                <p x-html = "name"></p>
            </div>
```
 x-html is similar as x-text but we should use x-text instead of x-html to prevent XSS attack.
 
 > ##  x-model 
 ```html
              <!-- Understanding how x-model works -->
              <div x-data = "{ name : '' }">
                  <input x-model="name"  placeholder="Enter your Name"/>
                   <p x-text = "'Hello '+ name"></p>
              </div>

                      <!-- handling radio buttons using x-model  -->
              <div x-data = "{gender : '',}">
                  <input  type="radio" value="Male" x-model="gender"/>
                  <label for="checkbox" >Male</label>
                         <br />
                  <input  type="radio" value="FeMale" x-model="gender"/>
                  <label for="checkbox" >FeMale</label>
                          <br />
                  <p x-text = "'Gender : ' + gender"></p>
              </div>

                      <!-- handling dropdown menu using x-model -->
              <div x-data = "{ color : '' }"> 
                  <select x-model="color">
                      <option>Red</option>
                      <option>Orange</option>
                      <option>Yellow</option>
                  </select>
                  <p x-text = " 'Your Color : '+ color "></p>
              </div>

                  <!-- only render value of input field to DOM when clicked outside not on every key stroke -->
              <div x-data = "{ name : '' }">
                  <input x-model.lazy="name"  placeholder="Enter your Name"/>
                   <p x-text = "'Hello '+ name"></p>
              </div>
 ```
x-model allows you to bind the value of an input element to Alpine data. when value will be changed then elements who
used x-model those will be re-rendered also. input field ,text area, checkbox, radio button, dropdown those things can be handled using
x-model. There are some other methods like .number (taking input as number), .debounce (calling after certain period of time) , .throttle
in the documentation , we can read basis on requirements.

> ##  x-for
```html
          <ul x-data="{ colors: ['Red', 'Orange', 'Yellow'] }">
            <template x-for="color in colors">
              <li x-text="color"></li>
            </template>
          </ul>
```

x-for MUST be declared on a <template> element
That <template> element MUST have only one root element.

> ## x-effect
```html
          <div x-data = "{ counter : 0 ,keep : 0}" x-effect = "console.log(counter)">
              <p x-text = " 'Counter Value : '+ counter "></p>
              <p x-text = " 'Keep Value : '+ keep "></p>
              <button class="bg-green-500"  @click="counter++">Counter+</button>
              <button class="bg-blue-500" @click="keep++">Keep+</button>
          </div>
```
x-effect is called everytime when the value is changed that it used inside its body.

> ## x-ref
```html
            <div x-data = "{ summation : 0 }">
                <input x-ref = "first" placeholder="First Number " />
                <input x-ref = "second" placeholder="Seconf Number " />
                 <p x-text = " 'Summation is : ' + summation "></p>
                <button @click = " 
                  summation = Number($refs.first.value)+ Number($refs.second.value)
                 ">
                 Add</button>
          </div>
```
x-ref in combination with $refs is a useful utility for easily accessing DOM elements directly. 
It's most useful as a replacement for APIs like getElementById and querySelector.

> ## $watch
```html
              <div x-data="{ open: false }" 
                   x-init="$watch('open', (value, oldValue) => console.log(value, oldValue))">
                  <button @click="open = ! open">Toggle Open</button>
              </div>
```
$watch can keep track of your old value and current value

> ## Making any Data reactive by Alpine
```html
         <button>Increment</button>

         Count: <span></span>
        <script>
          let button = document.querySelector('button')
          let span = document.querySelector('span')

          let data = Alpine.reactive({ count: 1 })

          Alpine.effect(() => {
            span.textContent = data.count
          })

          button.addEventListener('click', () => {
            data.count = data.count + 1
		})
	</script>

```




