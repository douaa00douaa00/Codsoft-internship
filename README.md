# Codsoft-internship_chatbot
from nltk.corpus import wordnet
import re

# List of words to find synonyms for 
list_words=['hello','timings','products','suggestions','order']
list_syn={}
for word in list_words:
    synonyms=[] 
    for syn in wordnet.synsets(word): 
        for lem in syn.lemmas():
            # Remove any special characters from synonym strings
            lem_name = re.sub('[^a-zA-Z0-9 \n.]', ' ', lem.name())
            
            synonyms.append(lem_name)
    list_syn[word]=set(synonyms)
    
keywords={}
keywords_dict={}

keywords['greet']=[]
# Populating the values in the keywords dictionary with synonyms of keywords formatted with RegEx metacharacters 
for synonym in list(list_syn['hello']):
    keywords['greet'].append('.*\\b'+synonym+'\\b.*')
    keywords['greet'].append('hey')


# Defining a new key in the keywords dictionary
keywords['timings']=[]
# Populating the values in the keywords dictionary with synonyms of keywords formatted with RegEx metacharacters 
for synonym in list(list_syn['timings']):
    keywords['timings'].append('.*\\b'+synonym+'\\b.*')


keywords['suggestions'] = []
suggestions_synonyms = ['suggest','propose','suggestions','proposal','proposition','propositions','recommendation ','advice'] 
for synonym in suggestions_synonyms:
    keywords['suggestions'].append('.*\\b' + synonym + '\\b.*')
# Compile regular expressions for the 'suggestions' intent
keywords_dict['suggestions'] = re.compile('|'.join(keywords['suggestions']))    

# Define synonyms for 'products' intent
keywords['products'] = []
products_synonyms = ['products','menu','catalogue','product','what you buy ','range'] 
for synonym in products_synonyms:
    keywords['products'].append('.*\\b' + synonym + '\\b.*')
# Compile regular expressions for the 'products' intent
keywords_dict['products'] = re.compile('|'.join(keywords['products']))



order_synonyms = ['order','purchase','buy','acquire','request','place an order','make an order','obtain','demand']
keywords['order'] = []
for synonym in order_synonyms:
    keywords['order'].append('.*\\b' + synonym + '\\b.*')
keywords_dict['order'] = re.compile('|'.join(keywords['order']))

    

for intent, keys in keywords.items():
    keywords_dict[intent] = re.compile('|'.join(keys))
#print (keywords_dict)
 

responses={
    'greet':'''ChatBOT :
                Hello! How can I help you?''',
    'timings':'''ChatBOT : ")
                We are open from 9AM to 5PM, Monday to Friday. We are closed on weekends and public holidays.''',
    'suggestions' :'''ChatBOT :
    Well, there's quite a variety of candies available! It all depends on your preferences. 
    Here is our Candy Selection:
                        1. Chocolatey Delight 
                            4. Chewy Caramel 
                                5. Minty Fresh 
                                    6. Sour Tangy Twists
                                        7. Gummy Bears .
                                             8. Licorice Sticks 
                                                 9. Jelly Beans
                                                     10. Lollipop Delight ''',
    'products' :'''ChatBOT :
    Here is all the options that are availabel in our *CanDy*SToRe*:  
    Chocolatey, Fruity, Nutty, Chewy, Minty, Salty-sweet and Tart''',
    'order':'''ChatBOT :
    Yes, of course! I'm here to assist you with your order. Please let me know what you'd like to order :''',
    'fallback':'''ChatBOT :
    I dont quite understand. Could you repeat that?'''
                    
}
print ("Welcome to *CanDy*SToRe* :). How may I help you? ")
# While loop to run the chatbot indefinetely
while (True):  
    print("You :")
    # Takes the user input and converts all characters to lowercase
    user_input = input().lower()

    # Defining the Chatbot's exit condition
    if (user_input == 'quit' or user_input == "by" or user_input=="good by"): 
        print ("ChatBOT : ")
        print ("Thank you for visiting ! .")
        break   
    matched_intent = None 
    if any(word in user_input.lower() for word in order_synonyms):
        print ("ChatBOT : ")
        print("Here is the candy selection that is available :")
        print('''Candy Selection:
                1. Chocolatey Delight - $1.50
                Creamy milk chocolate with a hint of hazelnut.

                2. Fruity Blast - $1.25
                Assorted fruity flavors bursting with sweetness.

                3. Nutty Crunch - $1.75
                Roasted peanuts coated in rich, creamy chocolate.

                4. Chewy Caramel - $1.50
                Soft caramel candies with a chewy texture.

                5. Minty Fresh - $1.50
                Refreshing mint candies for a cool burst of flavor.

                6. Sour Tangy Twists - $1.50
                Tangy and tart candies with a sour punch.

                7. Gummy Bears - $1.00
                Classic gummy bears in assorted flavors.

                8. Licorice Sticks - $1.25
                Traditional licorice sticks in black and red varieties.

                9. Jelly Beans - $1.00
                Colorful jelly beans in a variety of fruity flavors.

                10. Lollipop Delight - $1.50
                    Assorted lollipops in fun shapes and flavors.''')
        candy_selection = {
            "1": {"name": "Chocolatey Delight", "price": 1.50},
            "2": {"name": "Fruity Blast", "price": 1.25},
            "3": {"name": "Nutty Crunch", "price": 1.75},
            "4": {"name": "Chewy Caramel", "price": 1.50},
            "5": {"name": "Minty Fresh", "price": 1.50},
            "6": {"name": "Sour Tangy Twists", "price": 1.50},
            "7": {"name": "Gummy Bears", "price": 1.00},
            "8": {"name": "Licorice Sticks", "price": 1.25},
            "9": {"name": "Jelly Beans", "price": 1.00},
            "10": {"name": "Lollipop Delight", "price": 1.50}
        }
        # Initialize variables
        total_price = 0
        selected_items = []
        # Get user's choices
        choices = input("Enter the numbers of the candies you want to buy, separated by spaces: ").split()

        # Calculate total price and gather selected items
        for choice in choices:
            if choice in candy_selection:
                total_price += candy_selection[choice]["price"]
                selected_items.append(candy_selection[choice]["name"])

        # Output the selected items and total price
        if selected_items:
            print ("ChatBOT : ")
            print("Good choice! You selected:")
            for item in selected_items:
                print("-", item)
            print("Total price: $", total_price)
        else:
            print("No valid items selected.")
    else:
        for intent, pattern in keywords_dict.items():
            # Using the regular expression search function to look for keywords in user input
            if re.search(pattern, user_input): 
                # if a keyword matches, select the corresponding intent from the keywords_dict dictionary
                matched_intent = intent  

        # The fallback intent is selected by default
        key = 'fallback' 
        if matched_intent in responses:
            # If a keyword matches, the fallback intent is replaced by the matched intent as the key for the responses dictionary
            key = matched_intent
        # The chatbot prints the response that matches the selected intent
        print(responses[key])
    
