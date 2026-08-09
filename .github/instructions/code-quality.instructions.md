# Code Quality Instructions

**Applies to:** `app/models/**/*.rb`

This document provides detailed code quality guidelines for Ruby model files in this repository.

## Docstrings & Documentation

- Ensure all classes have docstrings explaining their purpose
- Document all public methods with:
  - A brief description of what the method does
  - Parameter types and descriptions using YARD syntax
  - Return type and description
  - Any exceptions that may be raised
  - Example usage for complex methods

Example (Ruby with YARD):
```ruby
# Calculates the total order value including tax and shipping
#
# @param items [Array<Item>] List of items in the order
# @param tax_rate [Float] Tax rate as a decimal (e.g., 0.08 for 8%)
# @param shipping_cost [Float] Shipping cost in dollars
# @return [Float] Total order value
# @raise [ArgumentError] if tax_rate is negative
#
# @example
#   order = Order.new
#   order.calculate_total(items, 0.08, 10.0) #=> 108.50
def calculate_total(items, tax_rate, shipping_cost)
  # implementation
end
```

## Ruby Style Guidelines

- Follow the Ruby Style Guide and community best practices
- Use 2-space indentation (never tabs)
- Keep lines under 120 characters
- Use meaningful variable and method names
- Prefer single quotes for strings unless interpolation is needed
- Use symbols for Hash keys

## ActiveRecord Models

- Keep models focused on a single responsibility
- Use scopes for common query patterns
- Leverage Rails conventions (naming, associations, validations)
- Validate input at the model level using Rails validators
- Use callbacks (`before_save`, `after_create`, etc.) sparingly
- Comment complex business logic

Example:
```ruby
class User < ApplicationRecord
  # Associations
  has_many :orders, dependent: :destroy
  
  # Validations
  validates :email, presence: true, uniqueness: true, format: { with: URI::MailTo::EMAIL_REGEXP }
  validates :name, presence: true, length: { minimum: 2, maximum: 100 }
  
  # Scopes
  scope :active, -> { where(active: true) }
  scope :created_today, -> { where('created_at >= ?', Time.current.beginning_of_day) }
  
  # Methods
  def full_display_name
    "#{name} (#{email})"
  end
end
```

## Avoid Code Smells

- **Avoid long methods** - break into smaller, focused methods
- **Avoid deeply nested conditionals** - use guard clauses or early returns
- **Avoid god objects** - keep models focused and delegate responsibilities
- **Avoid duplicate code** - extract into shared methods or concerns
- **Avoid magic numbers** - use named constants instead

Example of guard clauses:
```ruby
# ❌ AVOID: Deeply nested
def process_order(order)
  if order.valid?
    if order.items.any?
      if order.customer.has_payment_method?
        # process
      end
    end
  end
end

# ✅ PREFER: Guard clauses
def process_order(order)
  return unless order.valid?
  return unless order.items.any?
  return unless order.customer.has_payment_method?
  # process
end
```

## Testing Requirements

- Write unit tests for all models using RSpec or Minitest
- Test validations, associations, scopes, and business logic
- Aim for high code coverage (>80%)
- Use factories (FactoryBot) for test data instead of fixtures
- Test edge cases and error conditions

Example (RSpec):
```ruby
describe User do
  describe 'validations' do
    it { is_expected.to validate_presence_of(:email) }
    it { is_expected.to validate_uniqueness_of(:email) }
  end
  
  describe 'associations' do
    it { is_expected.to have_many(:orders).dependent(:destroy) }
  end
  
  describe '#full_display_name' do
    let(:user) { create(:user, name: 'John', email: 'john@example.com') }
    
    it 'returns name and email' do
      expect(user.full_display_name).to eq('John (john@example.com)')
    end
  end
end
```

## Naming Conventions

- Use `PascalCase` for class names: `UserProfile`, `OrderItem`
- Use `snake_case` for method and variable names: `user_name`, `calculate_total`
- Use `UPPER_SNAKE_CASE` for constants: `DEFAULT_TAX_RATE`, `MAX_RETRIES`
- Use descriptive names that indicate purpose: `active_users` instead of `users_list`

## Performance Considerations

- Use `.includes()` or `.preload()` to avoid N+1 queries
- Index frequently queried columns in the database schema
- Avoid eager-loading unnecessary associations
- Use database-level constraints where appropriate
- Monitor and optimize slow queries

Example:
```ruby
# ❌ AVOID: N+1 queries
users = User.all
users.each { |user| puts user.orders.count }

# ✅ PREFER: Eager load associations
users = User.includes(:orders)
users.each { |user| puts user.orders.count }
```

## DRY Principle (Don't Repeat Yourself)

- Extract common logic into shared methods
- Use Rails concerns for shared model behavior
- Create service objects for complex business logic
- Use modules to share functionality across models

Example with Concerns:
```ruby
# app/models/concerns/timestamps.rb
module Timestamps
  extend ActiveSupport::Concern
  
  def friendly_created_at
    created_at.strftime('%B %d, %Y')
  end
end

# app/models/user.rb
class User < ApplicationRecord
  include Timestamps
end
```
