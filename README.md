"""
Customer Support Ticket Analyzer
---------------------------------
A Python-based Ticket Analysis System that stores, cleans, analyses,
and extracts insights from customer support tickets.
"""

import string

# ============================================================
# STEP 1: Preloaded Tickets
# ============================================================

ticket_data = {
    'Ticket_No': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    'Customer_Name': [
        'Ravi', 'Meera', 'Sam', 'Anu', 'Rakesh',
        'Divya', 'Arjun', 'Kiran', 'Leela', 'Nisha'
    ],
    'Issue_Description': [
        ' Internet not working!!! ',
        'slow response, very poor service ',
        'GREAT support! issue resolved.',
        ' okay... need help ',
        'not BAD but slow',
        'Excellent guidance, Very Helpful!',
        'good support and good behaviour!',
        'Poor handling of technical issue',
        'Satisfied. Could be better.',
        'Good service... quick response.'
    ],
    'Priority': [
        'High', 'Low', 'High', 'Medium', 'Low',
        'High', 'Medium', 'High', 'Low', 'Medium'
    ]
}


def print_tickets(data, title="Ticket Data"):
    """Print ticket data in a readable, row-by-row format."""
    print(f"\n{'=' * 60}")
    print(f"{title}")
    print('=' * 60)
    for i in range(len(data['Ticket_No'])):
        print(f"Ticket No     : {data['Ticket_No'][i]}")
        print(f"Customer Name : {data['Customer_Name'][i]}")
        print(f"Issue         : {data['Issue_Description'][i]}")
        print(f"Priority      : {data['Priority'][i]}")
        print('-' * 60)


print("Initial Ticket Data (raw print):")
print(ticket_data)
print_tickets(ticket_data, "Initial Ticket Data (formatted)")


# ============================================================
# STEP 2: Add More Tickets
# ============================================================

def add_new_tickets(data):
    """Prompt the user to add new tickets with validation."""
    try:
        num_new = int(input("\nHow many new tickets do you want to add? "))
    except ValueError:
        print("Invalid number entered. Skipping ticket addition.")
        return data

    next_ticket_no = max(data['Ticket_No']) + 1 if data['Ticket_No'] else 1

    for i in range(num_new):
        print(f"\n--- New Ticket {i + 1} ---")
        name = input("Customer Name: ").strip()
        issue = input("Issue Description: ").strip()

        # Validate priority
        priority = input("Priority (High/Medium/Low): ").strip().capitalize()
        while priority not in ('High', 'Medium', 'Low'):
            print("Invalid priority. Please enter High, Medium, or Low.")
            priority = input("Priority (High/Medium/Low): ").strip().capitalize()

        data['Ticket_No'].append(next_ticket_no)
        data['Customer_Name'].append(name)
        data['Issue_Description'].append(issue)
        data['Priority'].append(priority)

        next_ticket_no += 1

    return data


# Uncomment the line below to interactively add tickets when running the script
# ticket_data = add_new_tickets(ticket_data)


# ============================================================
# STEP 3: Text Cleaning for Issue Descriptions
# ============================================================

SLANG_MAP = {
    'ok': 'okay',
    'bad': 'poor',   # example normalization; adjust as needed
}


def clean_text(text):
    """
    Clean a single issue description:
    - Remove punctuation (.,!?-)
    - Convert multiple spaces to a single space
    - Strip leading/trailing spaces
    - Lowercase the text
    - Replace slang/shorthand words
    """
    # Remove specified punctuation
    punctuation_to_remove = ".,!?-"
    for ch in punctuation_to_remove:
        text = text.replace(ch, '')

    # Lowercase
    text = text.lower()

    # Normalize multiple spaces -> single space, and strip
    words = text.split()
    text = ' '.join(words).strip()

    # Replace slang/shorthand words
    words = text.split()
    words = [SLANG_MAP.get(word, word) for word in words]
    text = ' '.join(words)

    return text


def clean_all_tickets(data):
    """Apply clean_text() to every issue description in the dataset."""
    data['Issue_Description'] = [clean_text(desc) for desc in data['Issue_Description']]
    return data


ticket_data = clean_all_tickets(ticket_data)
print_tickets(ticket_data, "Ticket Data After Text Cleaning")


# ============================================================
# STEP 4: Keyword-Based Issue Insights
# ============================================================

def count_tickets_with_word(word, data=ticket_data):
    """
    Case-insensitive search.
    Returns how many ticket descriptions contain the given word.
    """
    word = word.lower()
    count = 0
    for desc in data['Issue_Description']:
        if word in desc.lower().split():
            count += 1
    return count


print("\n" + "=" * 60)
print("Keyword-Based Issue Insights")
print("=" * 60)
for keyword in ["poor", "good", "slow", "excellent"]:
    print(f"Tickets containing '{keyword}': {count_tickets_with_word(keyword)}")


# ============================================================
# STEP 5: Final Summary & Insights
# ============================================================

# 1. Display Final Cleaned ticket_data
print_tickets(ticket_data, "Final Cleaned Ticket Data")

# 2. Priority Analysis
high_count = ticket_data['Priority'].count('High')
medium_count = ticket_data['Priority'].count('Medium')
low_count = ticket_data['Priority'].count('Low')

print("\n" + "=" * 60)
print("Priority Analysis")
print("=" * 60)
print(f"High Priority Tickets   : {high_count}")
print(f"Medium Priority Tickets : {medium_count}")
print(f"Low Priority Tickets    : {low_count}")

# 3. Find the Ticket With the Longest Issue Description
longest_index = 0
longest_word_count = 0

for i, desc in enumerate(ticket_data['Issue_Description']):
    word_count = len(desc.split())
    if word_count > longest_word_count:
        longest_word_count = word_count
        longest_index = i

print("\n" + "=" * 60)
print("Ticket With the Longest Issue Description")
print("=" * 60)
print(f"Ticket Number : {ticket_data['Ticket_No'][longest_index]}")
print(f"Customer Name : {ticket_data['Customer_Name'][longest_index]}")
print(f"Cleaned Issue : {ticket_data['Issue_Description'][longest_index]}")
print(f"Word Count    : {longest_word_count}")

# 4. Extract Unique Words Used
unique_words = set()
for desc in ticket_data['Issue_Description']:
    unique_words.update(desc.split())

sorted_unique_words = sorted(unique_words)

print("\n" + "=" * 60)
print("Unique Words Used Across All Issue Descriptions")
print("=" * 60)
print(f"Count of Unique Words: {len(sorted_unique_words)}")
print(f"Word List: {sorted_unique_words}")
