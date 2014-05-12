---
layout: post
title: "define_method vs method_missing"
date: 2014-05-11 10:31:50 -0400
comments: false
categories: "Ruby Meta&nbspProgramming method_missing Ghost&nbspMethods define_method Dynamic&nbspMethod&nbspDefinition"
---

##The Dark Arts of Programming
Before I delve into the differences between define_method and method_missing, I want to quickly talk about metaprogramming in general.  Metaprogramming is, at a most basic level, code that writes code.  This is powerful, but also potentially dangerous.  You could easily write a meta-method that will overwrite an integral method and break your program, or create subtle edge cases that throw cryptic errors.  Metaprogramming tricks are clever, but clever code means difficult code to test. So with these warnings in mind let's carefully explore define_method and method_missing.

##Define_method
Define_method is a way to dynamically define methods.

###How define_method Works
Define_method is fairly straight forward. It accepts a method name as an argument and is passed a block to be the block of the new method. (If you want to make a method directly on whatever object you are calling use define_singleton_method in the same way.) Here's an example,

```ruby
class TestClass 
  def self.make_instance_method(method_name)
    define_method(method_name.to_sym){puts "I'm a instance method!"}
  end
end

test_class = TestClass.new
test_class.test_method  # This method is not yet defined and will throw an error.
  #=> NoMethodError: undefined method `test_method' for #<TestClass:0x007fdd7ba0c1b0>
TestClass.make_instance_method(:test_method) # I just defined the method.
test_class.test_method # Now when I call it...
  #=> "I'm a instance method!"
```
###When to use define_method:
The ideal time to use this method is to define many, similar methods for an object. (By many, like ten or more.)  

###The Dangers of define_method
The reason to use it infrequently is that you have to be certain the reward of fewer, opaquer code is worth the risk of overwriting a vital method and then having to debug it.

The code below is an example from the code base of a project I am working on.

```ruby
class TempReadings

  METRICS = [:min, :max, :avg]    #these are the constants whose arrays have 
  CYCLES = [:day, :night]         #different parts of the method names in them.
  MEASUREMENTS = [:temp, :outdoor_temp]
  ...

  def metric_cycle_measures(metrics, cycles, measurements)
    metrics.each do |metric|
      cycles.each do |cycle|
        measurements.each do |measurement|
          define_method("#{metric}_#{cycle}_#{measurement}") do 
            array = self.send("get_cycle_#{measurement}s", cycle)
            self.send(metric, array)
          end
        end
      end
    end
  end
end
```
The code above is arguably unnecessary since it is only twelve methods wrapped into one. But we could also write this with method_missing, but it is also arguably unnecessary. 

##Method_missing
Method_missing is not a way to define a method, but, rather, a way to catch undefined methods that you would have liked to be defined.  The best way to use it is to respond to unknown method calls that are in some way predictable.  Think of column names being methods in ActiveRecord.

In the code above I know that the method name is a combination of a set number of words. I can refactor the code above by checking if a method is some combination of those names and respond accordingly. Here's what it looks like,

```ruby
class TempReadings

  def method_missing(name, *args)
    name_keywords = name.to_s.match(/(min|max|avg)_(day|night)_(temp|outdoor_temp)/)
    if name_keywords
      array = self.send("get_cycle_#{name_keywords[3]}s", name_keywords[2].to_sym)
      self.send(name_keywords[1].to_sym, array)
    else
      super #sends the failing method up the method look up chain.
    end
  end
end
```
###How method_missing Works
The code above duck punches the method_missing method for the class TempReadings.  It creates a MatchData object from the name passed as an argument.  If the name matches my regex expression it will then use the MatchData object to create methods on the fly to act accordingly.

###The Dangers of method_missing
This can be dangerous because if you leave out super to continue the possibly unknown method up the look up chain known methods won't work.  Conversely, you could catch unknown methods that you would want to throw errors that would instead work with unforeseen consequences. It could also catch methods higher up the chain and duck punch them similarly to define_method.

###When to use method_missing
Method_missing is really meant to be used when you are unclear of the method names.  This is rarely the case if it is ever your own project; instead it is often used in Ruby libraries or gems.