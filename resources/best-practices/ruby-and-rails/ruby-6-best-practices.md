
The Ruby language was developed by Yukihiro Matsumoto (“Matz”) in 1995. The language was designed to be object-oriented, intuitive, and easy to learn to optimize for developer time. As “Matz” once said:

> “…in fact we need to focus on humans, on how humans care about doing programming or operating the application of the machines. We are the masters. They are the slaves.”

In this tutorial, we’ll explore Ruby best practices to ensure our code is in line with Matz’s principles of being legible, modular, and concise.

1. Use case/when conditionals instead of lengthy if statements

Let’s say we are writing a program to ask for a student’s major and answer with an appropriate response. We have one variable, major, and we want to check its value and respond. We may be tempted to use an if statement with multiple elsifs. Avoid this temptation; there is a more concise way! The case/when statement allows you to check whether a variable is set to many different values and is particularly useful when responding to user input. Check it out:
```ruby
puts "What is your major?"
major = gets.chomp

case major
when "Biology"
	puts "Mmm the study of life itself!"
when "Computer Science"
	puts "I'm a computer!"
when "English"
	puts "No way! What's your favorite book?"
when "Math"
	puts "Sweet! I'm great with numbers!"
else
	puts "That's a cool major!"
end
```

The case/when statement is a concise way of  handling a variable with multiple possibilities.

2. Avoid for loops and use the each method with a block

While for loops are common and accepted in most other programming languages, in Ruby, we should almost universally avoid them. For loops are not as space efficient as using the each method and passing it as a block because they store the variable that represents each element. See the following example:
```ruby
for elem in [1, 2, 3, 4, 5]
puts elem
end
```

Now, after the loop is complete, if we check what elem is equal to:
```ruby
puts elem #=> 5
```

We didn’t actually want to store the value of elem, we just wanted to use it as a name placeholder for the current element while iterating. Instead, we use the more efficient each method with a block. (A block is Ruby’s version of an anonymous function or lambda). It is much better practice to write the above code as follows:
```ruby
[1, 2, 3, 4, 5].each do |elem|
	puts elem
end
```

Or even better, on one line:
```ruby
[1, 2, 3, 4, 5].each { |elem| puts elem }
```

Now, if we check what elem is:

puts elem #=> NameError: undefined local variable elem or method 'elem' for main:Object
Ahhh, that’s better!

3. Use the splat `(*)` operator for methods with a variable number of inputs

The main thing the splat operator is used for is grouping together a collection of inputs into an array. Before we dive into specifics of why it’s used, let’s see it in action.
```ruby
def mood(name, *feelings)
  feelings.each do |feeling|
    puts "#{name} is feeling #{feeling} today."
  end
end

feeling("Suzie", "happy", "excited", "nervous")
# Suzie is feeling happy today.
# Suzie is feeling excited today.
# Suzie is feeling nervous today.
```

People can feel many different things or they can just feel one thing. Because we have a variable number of inputs, we use the splat operator, which groups all of the inputs after the defined inputs into an array.  The splat operator also has some other fancy use cases. Let’s take a look at one of them.
```ruby
pablo_picasso_full_name = "Pablo Diego José Francisco de Paula Juan Nepomuceno María de los Remedios Cipriano de la Santísima Trinidad Ruiz y Picasso"

first, *other_names, last = pablo_picasso_full_name.split

first.join(" ")       #=> "Pablo"
other_names.join(" ") #=> "Diego José Francisco de Paula Juan Nepomuceno María de los Remedios Cipriano de la Santísima Trinidad Ruiz y"
last.join(" ")        #=> "Picasso"
```

(Yes, that’s Pablo Picasso’s full name!) Whenever we see a splat, we can just think of it as a way of wrangling a collection of an unknown number of items into an organized array so we can work with it more easily.

4. “Monkey patch” pre-defined methods so they work better for our specific needs

Let’s imagine we are designing a chess game. Our board is represented as an array of arrays, where each inner array is a row on the board. If we wanted to access a piece, we would get it like so: board[row][column]. We are going to be accessing pieces many times, and it would be easier if we could just call it like this instead: board[row, column]. So let’s take matters into our own hands and redefine the [] method!
```ruby
class Board < Array
	def [](row, column)
		self[row][column]
	end
end
```

We were able to do this because [] is really just a method on the Array class and our Board class inherits from Array. It is also easier to see that [] is just a method if we rewrite array[i] as array.[](i). Now, we can read what any piece is by just calling board[row, column]. But what if we also want to be able to move a piece by changing a value of board like this: board[row, column] = new_piece. To do this, we have to overwrite the []= method. See below.
```ruby
class Board
  attr_reader :a

  def initialize(height, width)
    @a=Array.new(height){Array.new(width)}
  end

  def [](row, column)
    @a[row][column]
  end

	def []=(row, column, new_piece)
		@a[row][column] = new_piece
	end

  def a
    @a
  end
end

a = Board.new(2,3)
a[0,0] = 1
a[1,0] = 1
a[0,0]
a[1,0]
a.a
```

Ruby is ultra object-oriented, which makes it quite easy to rewrite predefined methods so that they work better for us. Remember Matz’s ultimate goal? Keep the developer happy!

5. Use double bang to determine if a value exists

The bang symbol (!) turns any truthy value into false and any falsey value to true. Suppose we were trying to determine if a person has a middle name.
```ruby
class Name
  def initialize(first_name, last_name, middle_name=nil)
	   @first_name = first_name
	   @middle_name = middle_name
	    @last_name = last_name
  end

  def has_middle_name?
	   !!@middle_name
  end
end

Name.new("George", "Washington").has_middle_name?           #=> false
Name.new("Barack", "Obama", "Hussein").has_middle_name?     #=> true
```

This one is a bit tricky, so let’s walk through it step-by-step for the above examples. For the George Washington example, @middle_name = nil. Nil is not equal to false, but it is a falsey value. So, if we had just returned @middle_name without the !!, we would have gotten nil, not false like we wanted. Therefore, we use one bang to turn nil into true and then another bang to turn true into false. See below:
```ruby
	@middle_name #=> nil
	!@middle_name #=> true
	!!@middle_name #=> false
```

Likewise, Barack Obama’s middle name is “Hussein”, which is a truthy value. In the method has_middle_name?, we don’t want to return @middle_name, we just want to return true if @middle_name is truthy. See below for a walk through of how has_middle_name? works for the Barack Obama example.
```ruby
	@middle_name #=> "Hussein"
	!@middle_name #=> false
	!!@middle_name #=> true
```

If we hadn’t used a double bang, we would have had to use a wordy if statement to check if @middle_name is nil or not. The double bang is an elegant way of creating the common “does some variable exist?” method.

6. Use symbols instead of strings in hashes

Symbols are a Ruby specific data-type, denoted like so, :symbol. Symbols are similar to strings, except they are immutable and are used to name variables. They should be used whenever you are storing the name of something that does not have to be mutated. They are more efficient than strings because they are stored in one spot in memory.
```ruby
	a = :apple
	b = :apple
a.object_id == b.object_id    #=> true

	a = "apple"
	b = "apple"
a.object_id == b.object_id    #=> false
```

As we can see in the example above, when we set two variables equal to the same symbol, they point to the same address in memory. However, when we perform the same operation with a string, this is not the case. Thus, we should use symbols for improved space efficiency when we do not need to mutate its value. Now, let’s examine how we can use symbols as hash keys instead of strings. Let’s say we are trying to count how many of each plant we have in our garden and are keeping track in a hash. Instead of using strings like so,
```ruby
our_garden = { "roses" => 9, "rhododendrons" => 2,
"poppies" => 12, "geraniums" => 6 , "sneezeworts" => 5 }
```

we should use symbols:
```ruby
our_garden = { :roses => 9, :rhododendrons => 2,
:poppies => 12, :geraniums => 6, :sneezeworts => 5 }
```

Or even better, Ruby hashes that have symbols as keys can be written more concisely like so.
```ruby
our_garden = { roses: 9, rhododendrons: 2, poppies: 12, geraniums: 6, sneezeworts: 5 }
And when we’re trying to count rhododendrons and sneezeworts, it’s crucial that we are concise!
```

Conclusion

By keeping our code succinct and object-oriented, we can write programs in line with Matz’s philosophy that the development process should first and foremost be optimized for the developer, not the computer. And that’s one of several ways to know if you’re a bad developer or not. We are more efficient and happier if we are not bogged down with syntax and mundane specifics. When we are happier, we write better programs! Thank you Matz for keeping us happy! :)
