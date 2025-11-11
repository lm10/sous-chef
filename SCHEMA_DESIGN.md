# Restaurant MCP Server - JSON Schema Design

## Overview

This document defines the JSON schemas for the restaurant analytics MCP server data layer. The schemas are designed for:
- **Data Access**: pandas DataFrame operations
- **Scale**: 50-100 customers, 30-50 menu items
- **Storage Format**: JSON files
- **Analytics Focus**: Customer insights and menu performance

---

## 1. Customer JSON Schema

### File Location
[`data/customers/customers.json`](data/customers/customers.json)

### Schema Structure

```json
{
  "customers": [
    {
      "customer_id": "string (UUID format)",
      "name": "string",
      "email": "string (email format)",
      "phone": "string (E.164 format)",
      "total_orders": "integer (non-negative)",
      "lifetime_value": "number (non-negative, 2 decimal places)",
      "join_date": "string (ISO 8601 date format: YYYY-MM-DD)",
      "preferences": {
        "dietary_restrictions": ["string array (allergen names)"],
        "favorite_items": ["string array (item_id references)"]
      }
    }
  ]
}
```

### Field Definitions

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `customer_id` | string | Yes | UUID v4 format (e.g., "550e8400-e29b-41d4-a716-446655440000") | Unique customer identifier |
| `name` | string | Yes | 1-100 characters, non-empty | Customer's full name |
| `email` | string | Yes | Valid email format (RFC 5322) | Customer's email address |
| `phone` | string | Yes | E.164 format (e.g., "+14155552671") | Customer's phone number |
| `total_orders` | integer | Yes | >= 0 | Total number of orders placed |
| `lifetime_value` | number | Yes | >= 0.00, 2 decimal places | Total revenue from customer (in USD) |
| `join_date` | string | Yes | ISO 8601 date format (YYYY-MM-DD) | Date customer joined |
| `preferences` | object | Yes | Contains dietary_restrictions and favorite_items | Customer preferences |
| `preferences.dietary_restrictions` | array | Yes | Array of strings, can be empty | Allergens customer avoids |
| `preferences.favorite_items` | array | Yes | Array of item_id strings, can be empty | Customer's favorite menu items |

### Validation Rules

- **customer_id**: Must be unique across all customers, UUID v4 format
- **email**: Must be unique, valid email format
- **phone**: Must follow E.164 international format
- **total_orders**: Non-negative integer
- **lifetime_value**: Non-negative number with exactly 2 decimal places
- **join_date**: Must be valid date in YYYY-MM-DD format, not in future
- **dietary_restrictions**: Each item must match allergen names from menu schema
- **favorite_items**: Each item_id must reference valid menu items

### Example Customer Records

```json
{
  "customers": [
    {
      "customer_id": "550e8400-e29b-41d4-a716-446655440001",
      "name": "Sarah Johnson",
      "email": "sarah.johnson@email.com",
      "phone": "+14155551234",
      "total_orders": 47,
      "lifetime_value": 1245.50,
      "join_date": "2023-01-15",
      "preferences": {
        "dietary_restrictions": ["dairy", "gluten"],
        "favorite_items": ["item-001", "item-015", "item-023"]
      }
    },
    {
      "customer_id": "550e8400-e29b-41d4-a716-446655440002",
      "name": "Michael Chen",
      "email": "michael.chen@email.com",
      "phone": "+14155555678",
      "total_orders": 23,
      "lifetime_value": 678.25,
      "join_date": "2023-06-22",
      "preferences": {
        "dietary_restrictions": ["shellfish"],
        "favorite_items": ["item-005", "item-012"]
      }
    },
    {
      "customer_id": "550e8400-e29b-41d4-a716-446655440003",
      "name": "Emily Rodriguez",
      "email": "emily.rodriguez@email.com",
      "phone": "+14155559012",
      "total_orders": 89,
      "lifetime_value": 2456.75,
      "join_date": "2022-11-03",
      "preferences": {
        "dietary_restrictions": [],
        "favorite_items": ["item-003", "item-007", "item-018", "item-029"]
      }
    },
    {
      "customer_id": "550e8400-e29b-41d4-a716-446655440004",
      "name": "David Park",
      "email": "david.park@email.com",
      "phone": "+14155553456",
      "total_orders": 12,
      "lifetime_value": 345.00,
      "join_date": "2024-02-14",
      "preferences": {
        "dietary_restrictions": ["peanuts", "tree nuts"],
        "favorite_items": ["item-010"]
      }
    },
    {
      "customer_id": "550e8400-e29b-41d4-a716-446655440005",
      "name": "Lisa Thompson",
      "email": "lisa.thompson@email.com",
      "phone": "+14155557890",
      "total_orders": 156,
      "lifetime_value": 4123.80,
      "join_date": "2022-03-28",
      "preferences": {
        "dietary_restrictions": ["soy"],
        "favorite_items": ["item-002", "item-009", "item-014", "item-025", "item-031"]
      }
    }
  ]
}
```

---

## 2. Menu Items JSON Schema

### File Location
[`data/menu/menu_items.json`](data/menu/menu_items.json)

### Schema Structure

```json
{
  "menu_items": [
    {
      "item_id": "string (kebab-case format)",
      "name": "string",
      "category": "string (enum)",
      "price": "number (positive, 2 decimal places)",
      "description": "string",
      "allergens": ["string array"],
      "popularity_score": "number (0.0-100.0)",
      "available": "boolean"
    }
  ]
}
```

### Field Definitions

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `item_id` | string | Yes | Kebab-case format (e.g., "item-001"), unique | Unique menu item identifier |
| `name` | string | Yes | 1-100 characters, non-empty | Menu item name |
| `category` | string | Yes | One of predefined categories | Item category |
| `price` | number | Yes | > 0.00, 2 decimal places | Item price in USD |
| `description` | string | Yes | 1-500 characters | Item description |
| `allergens` | array | Yes | Array of strings from standard list, can be empty | Allergens present in item |
| `popularity_score` | number | Yes | 0.0 to 100.0, 1 decimal place | Popularity metric |
| `available` | boolean | Yes | true or false | Current availability status |

### Category Enum Values

The `category` field must be one of the following values:

- `appetizer` - Starters and small plates
- `main` - Main course dishes
- `dessert` - Desserts and sweet items
- `beverage` - Drinks (alcoholic and non-alcoholic)
- `side` - Side dishes and accompaniments
- `special` - Chef's specials and seasonal items

### Standard Allergen List

The `allergens` array items must be from this standardized list:

- `dairy` - Milk and dairy products
- `eggs` - Eggs and egg products
- `fish` - Fish and fish products
- `shellfish` - Crustaceans and mollusks
- `tree nuts` - Almonds, cashews, walnuts, etc.
- `peanuts` - Peanuts and peanut products
- `wheat` - Wheat and wheat products
- `gluten` - Gluten-containing grains
- `soy` - Soy and soy products
- `sesame` - Sesame seeds and oil

### Popularity Score Definition

The `popularity_score` is a composite metric (0.0-100.0) that represents:
- **0.0-20.0**: Low popularity (rarely ordered)
- **20.1-40.0**: Below average popularity
- **40.1-60.0**: Average popularity
- **60.1-80.0**: Above average popularity
- **80.1-100.0**: High popularity (frequently ordered)

The score can be calculated based on:
- Order frequency
- Customer ratings
- Revenue contribution
- Reorder rate

### Validation Rules

- **item_id**: Must be unique across all menu items, kebab-case format
- **name**: Non-empty string
- **category**: Must be one of the defined enum values
- **price**: Positive number with exactly 2 decimal places
- **description**: Non-empty string, maximum 500 characters
- **allergens**: Each item must be from the standard allergen list
- **popularity_score**: Number between 0.0 and 100.0 (inclusive)
- **available**: Boolean value only

### Example Menu Item Records

```json
{
  "menu_items": [
    {
      "item_id": "item-001",
      "name": "Grilled Salmon",
      "category": "main",
      "price": 28.50,
      "description": "Fresh Atlantic salmon grilled to perfection, served with seasonal vegetables and lemon butter sauce",
      "allergens": ["fish", "dairy"],
      "popularity_score": 87.5,
      "available": true
    },
    {
      "item_id": "item-002",
      "name": "Caesar Salad",
      "category": "appetizer",
      "price": 12.00,
      "description": "Classic Caesar salad with romaine lettuce, parmesan cheese, croutons, and house-made Caesar dressing",
      "allergens": ["dairy", "eggs", "gluten", "fish"],
      "popularity_score": 72.3,
      "available": true
    },
    {
      "item_id": "item-003",
      "name": "Chocolate Lava Cake",
      "category": "dessert",
      "price": 9.50,
      "description": "Warm chocolate cake with a molten chocolate center, served with vanilla ice cream",
      "allergens": ["dairy", "eggs", "gluten", "soy"],
      "popularity_score": 94.2,
      "available": true
    },
    {
      "item_id": "item-004",
      "name": "Thai Peanut Noodles",
      "category": "main",
      "price": 16.75,
      "description": "Rice noodles tossed in creamy peanut sauce with vegetables, garnished with crushed peanuts and cilantro",
      "allergens": ["peanuts", "soy", "sesame"],
      "popularity_score": 45.8,
      "available": false
    },
    {
      "item_id": "item-005",
      "name": "Craft IPA",
      "category": "beverage",
      "price": 8.00,
      "description": "Local craft India Pale Ale with citrus and pine notes, 16oz pour",
      "allergens": ["gluten"],
      "popularity_score": 68.9,
      "available": true
    }
  ]
}
```

---

## 3. Design Decisions

### Data Type Choices

#### Customer Schema

1. **customer_id as UUID**
   - **Rationale**: Provides globally unique identifiers, prevents collisions when scaling
   - **Format**: UUID v4 standard
   - **Pandas compatibility**: Stored as string, easily indexed

2. **Monetary values as numbers with 2 decimal places**
   - **Rationale**: Standard for USD currency, prevents floating-point precision issues
   - **Format**: Always 2 decimal places (e.g., 123.45, not 123.4)
   - **Pandas compatibility**: float64 type in pandas

3. **Dates as ISO 8601 strings (YYYY-MM-DD)**
   - **Rationale**: Unambiguous, sortable, internationally recognized
   - **Pandas compatibility**: Easily converted to datetime64 with `pd.to_datetime()`
   - **Benefits**: Supports date arithmetic and filtering

4. **Phone numbers in E.164 format**
   - **Rationale**: International standard, unambiguous
   - **Format**: +[country code][number] (e.g., +14155551234)
   - **Benefits**: Consistent length checking, international support

5. **Preferences as nested object**
   - **Rationale**: Logical grouping of related data
   - **Pandas compatibility**: Can be accessed via dot notation or as dict
   - **Extensibility**: Easy to add more preference fields later

6. **Arrays for multi-value fields**
   - **Rationale**: Natural way to store multiple values (allergens, favorites)
   - **Pandas compatibility**: Works with pandas Series, can explode for analysis
   - **Queryability**: Easy to filter and aggregate

#### Menu Schema

1. **item_id as kebab-case string**
   - **Rationale**: Human-readable, URL-safe, consistent format
   - **Format**: "item-XXX" where XXX is zero-padded number
   - **Benefits**: Easy to reference, self-documenting

2. **Category as string enum**
   - **Rationale**: Provides clear categories without complex lookup tables
   - **Pandas compatibility**: Can be converted to categorical dtype for efficiency
   - **Benefits**: Easy filtering and grouping

3. **Popularity score as float (0.0-100.0)**
   - **Rationale**: Intuitive percentage-like scale
   - **Precision**: 1 decimal place for adequate granularity
   - **Benefits**: Easy to interpret, compare, and visualize

4. **Boolean for availability**
   - **Rationale**: Simple binary state
   - **Pandas compatibility**: Native boolean type
   - **Benefits**: Fast filtering, clear semantics

### Optional vs Required Fields

**All fields are required** in both schemas because:
- Small dataset size (50-100 customers, 30-50 items) makes completeness manageable
- Analytics quality depends on complete data
- Missing values complicate pandas operations and calculations
- Better to use default values (e.g., empty arrays) than null/None

**Exception handling**: If a field is truly unknown:
- Arrays: Use empty array `[]`
- Numbers: Use `0` or `0.0` as appropriate
- Strings: Use empty string `""` only if semantically valid

### Data Relationships

1. **Customer → Menu Items (via preferences.favorite_items)**
   - Type: Many-to-many relationship
   - Implementation: Array of item_id references in customer record
   - Validation: All item_ids must exist in menu_items
   - Benefits: Enables customer preference analysis

2. **Customer → Allergens (via preferences.dietary_restrictions)**
   - Type: Cross-reference to menu allergens
   - Implementation: Same allergen vocabulary in both schemas
   - Validation: Customer allergens must be from standard list
   - Benefits: Can recommend safe menu items to customers

3. **Referential Integrity**
   - Favorite items must reference valid item_ids
   - Allergen names must match standard list
   - No orphaned references allowed

---

## 4. JSON Schema Documentation

### Overall Structure

Both JSON files use a root object with an array property:

**Customers**:
```json
{
  "customers": [/* array of customer objects */]
}
```

**Menu Items**:
```json
{
  "menu_items": [/* array of menu item objects */]
}
```

This structure:
- Allows for future metadata at root level
- Makes array iteration explicit
- Supports JSON schema validation
- Works seamlessly with pandas: `pd.read_json(file)['customers']`

### Pandas Loading Pattern

```python
import pandas as pd
import json

# Load customers
with open('data/customers/customers.json') as f:
    data = json.load(f)
customers_df = pd.DataFrame(data['customers'])

# Convert date string to datetime
customers_df['join_date'] = pd.to_datetime(customers_df['join_date'])

# Load menu items
with open('data/menu/menu_items.json') as f:
    data = json.load(f)
menu_df = pd.DataFrame(data['menu_items'])

# Convert category to categorical for efficiency
menu_df['category'] = menu_df['category'].astype('category')
```

### Example Analytics Queries

#### Customer Analytics

1. **Find high-value customers**:
```python
high_value = customers_df[customers_df['lifetime_value'] > 1000.00]
```

2. **Customers with specific dietary restrictions**:
```python
dairy_free = customers_df[
    customers_df['preferences'].apply(
        lambda x: 'dairy' in x['dietary_restrictions']
    )
]
```

3. **Average order value by customer**:
```python
customers_df['avg_order_value'] = (
    customers_df['lifetime_value'] / customers_df['total_orders']
)
```

4. **Customer tenure analysis**:
```python
customers_df['days_since_join'] = (
    pd.Timestamp.now() - customers_df['join_date']
).dt.days
```

5. **Most popular favorite items across all customers**:
```python
from collections import Counter

all_favorites = []
for prefs in customers_df['preferences']:
    all_favorites.extend(prefs['favorite_items'])

favorite_counts = Counter(all_favorites)
top_favorites = favorite_counts.most_common(10)
```

#### Menu Analytics

1. **Items by category**:
```python
category_counts = menu_df.groupby('category').size()
```

2. **Average price by category**:
```python
avg_prices = menu_df.groupby('category')['price'].mean()
```

3. **High popularity items**:
```python
popular_items = menu_df[menu_df['popularity_score'] >= 80.0]
```

4. **Available items without specific allergen**:
```python
dairy_free_menu = menu_df[
    menu_df['available'] & 
    ~menu_df['allergens'].apply(lambda x: 'dairy' in x)
]
```

5. **Price distribution by category**:
```python
price_stats = menu_df.groupby('category')['price'].describe()
```

#### Cross-Schema Analytics

1. **Safe menu recommendations for customer**:
```python
def get_safe_items(customer_id, customers_df, menu_df):
    customer = customers_df[customers_df['customer_id'] == customer_id].iloc[0]
    restrictions = customer['preferences']['dietary_restrictions']
    
    safe_items = menu_df[
        menu_df['available'] & 
        menu_df['allergens'].apply(
            lambda x: not any(allergen in x for allergen in restrictions)
        )
    ]
    return safe_items
```

2. **Analyze favorite item patterns**:
```python
# Get all favorite items with their details
favorites_expanded = []
for _, customer in customers_df.iterrows():
    for item_id in customer['preferences']['favorite_items']:
        item = menu_df[menu_df['item_id'] == item_id].iloc[0]
        favorites_expanded.append({
            'customer_id': customer['customer_id'],
            'item_id': item_id,
            'category': item['category'],
            'price': item['price']
        })

favorites_df = pd.DataFrame(favorites_expanded)
category_preferences = favorites_df.groupby('category').size()
```

3. **Revenue potential from favorites**:
```python
# Calculate potential revenue if all customers ordered their favorites
def calculate_favorites_revenue(customers_df, menu_df):
    total_potential = 0
    for _, customer in customers_df.iterrows():
        for item_id in customer['preferences']['favorite_items']:
            item = menu_df[menu_df['item_id'] == item_id].iloc[0]
            total_potential += item['price']
    return total_potential
```

---

## 5. Implementation Checklist

### Data File Creation
- [ ] Create [`data/customers/customers.json`](data/customers/customers.json) with 50-100 customer records
- [ ] Create [`data/menu/menu_items.json`](data/menu/menu_items.json) with 30-50 menu item records
- [ ] Ensure all item_id references in customer favorites are valid
- [ ] Ensure all allergen names match the standard list

### Validation
- [ ] Verify all customer_ids are unique UUIDs
- [ ] Verify all emails are valid and unique
- [ ] Verify all dates are in YYYY-MM-DD format
- [ ] Verify all monetary values have exactly 2 decimal places
- [ ] Verify all phone numbers are in E.164 format
- [ ] Verify all item_ids are unique and follow kebab-case pattern
- [ ] Verify all categories match enum values
- [ ] Verify all popularity scores are between 0.0 and 100.0
- [ ] Verify referential integrity (favorite_items reference valid item_ids)

### Testing
- [ ] Test pandas loading for both files
- [ ] Test data type conversions (dates, categories)
- [ ] Test example queries from documentation
- [ ] Test cross-schema queries
- [ ] Verify no missing or null values

---

## 6. Schema Benefits

### Analytics-Ready
- Clean, consistent data types optimized for pandas
- Standardized formats enable reliable calculations
- No null values to handle in basic operations
- Relationships clearly defined for joins

### Maintainable
- Self-documenting field names
- Clear validation rules
- Standard formats (ISO 8601, E.164, UUID)
- Extensible structure for future enhancements

### Scalable
- Efficient categorical types for enums
- UUID support for distributed systems
- Array structures support multiple values
- Schema can grow from 50-100 to thousands without structural changes

### Query-Friendly
- Fast filtering with boolean flags (available)
- Easy grouping by categorical fields (category)
- Range queries on numeric scores (popularity_score)
- Date-based filtering and analysis (join_date)

---

## Conclusion

These JSON schemas provide a robust foundation for the restaurant analytics MCP server. They are:
- **Pandas-optimized** for efficient data operations
- **Validation-ready** with clear constraints
- **Analytics-focused** supporting common queries
- **Production-ready** with proper data types and relationships

The schemas balance simplicity with functionality, providing just enough structure for meaningful analytics while remaining easy to implement and maintain.