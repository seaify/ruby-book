# action_mailer
```ruby
Depot::Application.configure do config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
:address
:port
:domain
:authentication
:user_name
:password
:enable_starttls_auto => true
=> "smtp.gmail.com",
=> 587,
=> "domain.of.sender.net", => "plain",
=> "dave",
=> "secret",
}
end

rails generate mailer Notifier order_received order_shipped


app/mailers

class NotifierTest < ActionMailer::TestCase test "order_received" do
mail = Notifier.order_received(orders(:one))
assert_equal "Pragmatic Store Order Confirmation", mail.subject assert_equal ["dave@example.org"], mail.to
assert_equal ["depot@example.com"], mail.from
assert_match /1 x Programming Ruby 1.9/, mail.body.encoded
end

config.autoload_paths += %W(#{Rails.root}/lib)

we might have a PDF receipt writer in the file receipt.rb in the directory lib/pdf_stuff. As long as our class is named PdfStuff::Receipt, Rails will be able to find and load it automatically.

they also contain timing statistics, cache information, and expansions of the database statements executed.

rails generate controller Admin::Book action1 action2

The value returned by the attribute readers is cast by Active Record to an appropriate Ruby type if possible (so, for example, if the database column is a timestamp, a Time object will be returned). If we want to get the raw value of an attribute, we append _before_type_cast to its name, as shown in the following code:

total =  Product.find(product_list).sum(&:price)

order = Order.find_by_name("Dave Thomas")
orders = Order.find_all_by_name("Dave Thomas")


Appending a bang (!) character to the find_by_ call will cause a ActiveRecord:: RecordNotFound exception to be raised instead of returning nil if it can’t find a matching record:
order = Order.find_by_name!("Dave Thomas")

We can take this a step further. Because params is effectively a hash, we can simply pass it all to the condition. If we have a form that can be used to enter search criteria, we can use the hash of values returned from that form directly:
pos = Order.where("name = :name and pay_type = :pay_type", params[:order])


• 2.years.ago
• [1,2,3,4].sum
• 5.gigabytes
• "man".pluralize
• String.methods.sort

````