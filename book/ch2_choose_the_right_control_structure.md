Choose the Right Control Structure
==================================

## If, Unless, while, and Until ##
```ruby
class Document
	attr_accessor :writable
	attr_reader :title, :author, :content

	#...

	def title=(new_title)
		if @writable
			@title = new_title
		end
	end

	#...
end
```

일반적으로 Ruby 에서 If 구문을 위와 같이 쓴다. 하지만 @writable 대신에 @read_only (반대값)을 조건으로 준다면 다음과 같이 바뀌어야 할 것이다.

```ruby
def title=(new_title)
	if not @read_only
		@title = new_title
	end
end
```

Ruby 에서 If not 을 지원하지만 좀더 관용적으로 쓰기 위해선

```ruby
def title=(new_title)
	unless @read_only
		@title = new_title
	end
end
```

Unless 를 사용해주는 게 좋다. 여기에는 두가지 장점이 있다.

1. if not 보다 짧고 간결하다.
2. 코드를 읽고 이해하는데 훨씬 쉽다.

While 문에서도 다음과 같이 코드를 쓸 수 있는데

```ruby
while ! document.is_printed?
	document.print_next_page
end
```

다음과 같이 Until 을 이용해서 더 이해하기 쉽게 바꾸는 게 좋다.

```ruby
unless document.is_printed?
	document.print_next_page
end
```

조건절을 한줄로 좀 더 간결하게 읽기 용이하게 다음과 같이 쓸 수 있다.

```ruby
@title = new_title unless @read_only
@title = new_title if @read_only
document.print_next_page while document.pages_available?
document.print_next_page unless document.printed?
```

## Use each, Not for ##

Ruby 에서는 for loop 을 제공하지만 관용적으로 each 를 더 선호한다.

```ruby
fonts = ['돋움','고딕','굴림']

for font in fonts
	puts fonts
end
```

보다는

```ruby
fonts = ['돋움','고딕','굴림']

fonts.each do |font|
	puts font
end
```

이 선호된다. 자세한 내용에 대해서 다음 글을 참조하길 바란다. [the evils of the for loop](http://graysoftinc.com/early-steps/the-evils-of-the-for-loop)

## A Case of Programming Logic ##

다른 언어에서 사용되는 switch 문을 Ruby 에서는 case, when 으로 아래와 같이 쓸 수 있다.

```ruby
case title
when 'War And Peace'
	puts 'Tolstoy'
when 'Romeo And Juliet'
	puts 'Shakespeare'
else
	puts "Don't know"
end
```

case 조건에서 값을 리턴받을 수도 있다.

```ruby
author = case title
		 when 'War And Peace'
		 	 'Tolstoy'
		 when 'Romeo And Juliet'
			 'Shakespeare'
		 else
			 "Don't know"
		 end
```

else 가 없다면 nil 이 리턴이 된다. Type 을 이용해서 조건을 명시할 수도 있다.

```ruby
case doc
when Document
	puts "It's Document"
when String
	puts "It's a String"
else
	puts "Don't know"
end
```

정규표현식을 이용할 수도 있다.

```ruby
case title                       
when /War And .*/
  puts 'Maybe Tolstoy?'
when /Romeo And .*/
  puts 'Maybe Shakespeare?'
else
  puts 'Absolutely no idea...'
end  
```

case 문에 조건을 안넣고 when 절마다 값을 비교할 수도 있다.

```ruby
case
when title == 'War And Peace'
  puts 'Tolstoy'
when title == 'Romeo And Juliet'
  puts 'Shakespeare'
else
  puts 'Absolutely no idea...'
end
```

## Staying Out of Trouble ##

Ruby 에서 boolean logic 을 다룰때 주의할 점이 있다. Ruby 에서는 false 와 nil 만이 false 로 판단되고 0 이나 '' 빈문자 등은 전부 true 로 판단이 된다. 그래서

```ruby
puts 'Sorry Dennis Ritchie, but 0 is true!' if 0 # 이 코드는 실행이된다. 0 은 true 이기 때문에
puts 'Sorry but "false" is not false' if 'false' # 마찬가지로 true 다 문자열이기 때문에
```

결국 두가지 (false,nil) 외에 모든 값(숫자, 문자 등)은 true 가 되기 때문에 동적타이핑이 되는 Ruby 언어 특성상 true 를 비교할때 아래와 같이 명시하는게 안전하다.

```ruby
if flag == true
	# do something
end
```

nil 값을 반환하는 예제에서도 버그가 발생할 수 있다. get_next_object 가 다음 객체를 가져오는 메서드라고 해보자.

```ruby
while next_object = get_next_object
	# Do something with the object
end
```

next_object 에 nil 값이 전달되면 반복은 멈추겠지만 만약에 그 객체가 false 값이라면 또한 반복을 멈추게 된다. 그래서 확실히 nil 체크를 해주는 게 좋다.

```ruby
while (next_object = get_next_object) == nil # 또는 (next_object = get_next_object).nil?
	# Do something with the object
end
```

## In the Wild ##
Ruby 에서는 if 문을 컴팩트하게 쓰기 위해 ?: 연산자 (삼항연산자)도 많이 쓰인다.

```ruby
file = all ? 'specs' : 'latest_specs'
```

변수의 초기화 값을 지정해줄때 다음과 같은 방법이 많이 쓰인다.
```ruby
@first_name ||= ''
```