# Protokoll [![Travis](https://api.travis-ci.org/celsodantas/protokoll.png)](http://travis-ci.org/celsodantas/protokoll) [![Gem Version](https://badge.fury.io/rb/protokoll.svg)](http://badge.fury.io/rb/protokoll)


## Description

ravis Gem Version Protocol is a straightforward Rails 4 plugin designed to simplify the management of a custom autoincrement value for a model.

If you need to create an autoincrement information in your database, similar to call center registration numbers (e.g., 2011000001, 2011000002, 20110000003, and so on), this gem is the perfect solution for you! Whether you want to create a custom autoincrement value, this gem has got you covered.

Say goodbye to the complexities of managing counters, especially when you need to reset them monthly. With Protokoll, all you need to do is define a String column, and it will seamlessly handle the rest for you.







```ruby
 # creating an autoincrement column based on Time
class Call < ActiveRecord::Base
    protokoll :registry_number  # by default it uses "%Y%m#####"
end

Time.local(2011)
call01 = Call.create
call01.registry_number
=> "201100001"

call02 = Call.create
call02.registry_number
=> "201100002"

Time.local(2012)

call03 = Call.create
call03.registry_number
=> "201200001"  # restarts counter because it is the first item of the year
```

If you want to use your own pattern, just do this:

```ruby
class Call < ActiveRecord::Base
    protokoll :registry_number, :pattern => "some#####thing"
end

# this will produce
call01 = Call.create
call01.registry_number
=> "some00001thing"

call02 = Call.create
call02.registry_number
=> "some00002thing"
```

Or use any time based format. You can use in the pattern any combination of:

```ruby
# assume it's 2011/01/01 12:00
"%Y" for year   	# => appends 2011
"%y" for year   	# => appends 11
"%m" for month  	# => appends 01
"%d" for day	 	# => appends 01
"%H" for hour	 	# => appends 12
"%M" for minute 	# => appends 00
"#"  for the autoincrement number (use as long as you want)
```

Using the Time formating string ("%y", "%m", ...) is totally optional. It's fine to use "CALL####", "BUY###N" or any combination you like.

Ex:
```ruby
# :number must be a String
class Car < ActiveRecord::Base
    protokoll :number, :pattern => "CAR%y#####"
end

# will produce => "CAR1100001", "CAR1100002"...

# :sell_number must be a String
class House < ActiveRecord::Base
    protokoll :sell_number, :pattern => "%YHOUSE#####"
end

# will produce => "2011HOUSE00001", "2011HOUSE00002"...
```

It's possible to pass :scope_by option as a simple method, Proc.new or lambda

Ex:
```ruby
# :manufacturer should be a Car's instance method(like an ActiveRecord column)
class Car < ActiveRecord::Base
    protokoll :code, :scope_by => :manufacturer
end
# will scope Cars by manufacturers, for example "Ford", "Chevrolet"

# :manufacturer and :year should be Car's instance methods(like ActiveRecord columns)
class Car < ActiveRecord::Base
    protokoll :code, :scope_by => lambda { |o| "#{o.manufacturer}-#{o.year}" }
end
# will scope Cars by for example "Ford-2016"

# :manufacturer and :year should be Car's instance methods(like ActiveRecord columns)
class Car < ActiveRecord::Base
    protokoll :code, :scope_by => Proc.new{ "#{manufacturer}-#{model}" }
end
# will scope Cars by for example "Ford-Mustang", "Chevrolet-Camaro"
```


## reserve_number!

   object.reserve_number!

Add a new intance method colled: "your_instance#reserve_#{column_name}!".
With it you can reserve a number without the need to save it to the database. Ex:

```ruby
 car = Car.new
 car.number
 # => nil
 car.reserve_number!
 car.number
 # => "CAR1100001"
 # if you save it, the object will preserve the number: "CAR1100001"
```

It just increases the counter so any other object that gets saved or uses the #reserve_#{column_name} will get the next available number.




## Questions & Sugestions
This is my _first_ public gem, so if you have any questions os sugestions, feel free to contact me (use github msg system for that). It will be awesome to hear feedback to improve the code.

The gem is still on early _alpha_ so you may find bugs on it. And if you do, please use the _Issues_ here in Github and I'd love to fix it.

This piece of software is free to use.

Check the wiki if you are having any issues while upgrading from version `0.x`

