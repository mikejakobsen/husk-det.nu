# Fuch yea' Swift!

Swift REPL
  
  Read, Eval, Print, Loop


    xcrun swift

Homebrew installs Swift to

    /usr/local/opt/swift/bin/swift


Let = constant

var = variable

    Use constant as default. Unless you know you're gonna change the value later on. 

## What is a tuples?

´´
    class Person {
        var firstName: String
        var lastName: String
        
        init(firstName: String, lastName: String) {
            self.firstName = firstName
            self.lastName = lastName
        }
        
        func say(phrase: String) {
            print("\(firstName) \(lastName) says '\(phrase)'")
        }
    }

    let p1 = Person(firstName: "Alex", lastName: "Vollmer")
    p1.say("Let's write some !swift")
´´

## Functions

´´
	func say(phrase: String) {
    print(phrase)
	}

	say("Hej")
´´

´´
	func say(phrase: String, times: Int) {
		for _ in 0...times {
		print(phrase)
		}
	}

	say("Hej", times: 2)
´´

## Optionals


