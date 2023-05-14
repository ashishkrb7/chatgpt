# Transforming

In this notebook, we will explore how to use Large Language Models for text transformation tasks such as language translation, spelling and grammar checking, tone adjustment, and format conversion.

## Setup


```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_type = os.getenv("api_type")
openai.api_base = os.getenv("api_base")
openai.api_version = os.getenv("api_version")
openai.api_key = os.getenv("OPENAI_API_KEY")
```


```python
def get_completion(prompt, model="chatgpt-gpt35-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        engine=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
        max_tokens=800,
        top_p=0.95,
        frequency_penalty=0,
        presence_penalty=0,
        stop=None)
    return response.choices[0].message["content"]
```

## Translation

ChatGPT is trained with sources in many languages. This gives the model the ability to do translation. Here are some examples of how to use this capability.


```python
prompt = f"""
Translate the following English text to Hindi: \ 
```Hi, I would like to order a blender```
"""
response = get_completion(prompt)
print(response)
```

    नमस्ते, मैं एक ब्लेंडर ऑर्डर करना चाहूँगा।
    


```python
prompt = f"""
Tell me which language this is: 
```नमस्ते, मैं एक ब्लेंडर ऑर्डर करना चाहूँगा।```
"""
response = get_completion(prompt)
print(response)
```

    This is Hindi.
    


```python
prompt = f"""
Translate the following  text to Hindi and Bengali
and English pirate: \
```I want to order a basketball```
"""
response = get_completion(prompt)
print(response)
```

    Hindi: मैं एक बास्केटबॉल आदेश करना चाहता हूँ।
    Bengali: আমি একটি বাস্কেটবল অর্ডার করতে চাই।
    English Pirate: ```Arrr, I be wantin' to order a basketball, matey!```
    


```python
prompt = f"""
Translate the following text to Hindi in both the \
formal and informal forms: 
'Would you like to order a pillow?'
"""
response = get_completion(prompt)
print(response)
```

    Formal: क्या आप एक तकिया आर्डर करना चाहेंगे?
    Informal: क्या तुम एक तकिया आर्डर करना चाहोगे?
    

### Universal Translator
Imagine you are in charge of IT at a large multinational e-commerce company. Users are messaging you with IT issues in all their native languages. Your staff is from all over the world and speaks only their native languages. You need a universal translator!


```python
user_messages = [
  "La performance du système est plus lente que d'habitude.",  # System performance is slower than normal         
  "Mi monitor tiene píxeles que no se iluminan.",              # My monitor has pixels that are not lighting
  "Il mio mouse non funziona",                                 # My mouse is not working
  "Mój klawisz Ctrl jest zepsuty",                             # My keyboard has a broken control key
  "我的屏幕在闪烁"                                               # My screen is flashing
] 
```


```python
for issue in user_messages:
    prompt = f"Tell me what language this is: ```{issue}```"
    lang = get_completion(prompt)
    print(f"Original message ({lang}): {issue}")

    prompt = f"""
    Translate the following  text to English \
    and Korean: ```{issue}```
    """
    response = get_completion(prompt)
    print(response, "\n")
```

    Original message (This is French.): La performance du système est plus lente que d'habitude.
    English: The system performance is slower than usual.
    Korean: 시스템 성능이 평소보다 느립니다. 
    
    Original message (This is Spanish.): Mi monitor tiene píxeles que no se iluminan.
    English: My monitor has pixels that don't light up.
    Korean: 내 모니터에는 불이 켜지지 않는 픽셀이 있습니다. 
    
    Original message (This is Italian.): Il mio mouse non funziona
    English: My mouse is not working.
    Korean: 내 마우스가 작동하지 않습니다. 
    
    Original message (This is Polish.): Mój klawisz Ctrl jest zepsuty
    English: My Ctrl key is broken.
    Korean: 제 Ctrl 키가 고장 났어요. 
    
    Original message (This is Chinese (Simplified).): 我的屏幕在闪烁
    English: My screen is flickering.
    Korean: 내 화면이 깜빡입니다. 
    
    

## Tone Transformation
Writing can vary based on the intended audience. ChatGPT can produce different tones.



```python
prompt = f"""
Translate the following from slang to a business letter: 
'Dude, This is Joe, check out this spec on this standing lamp.'
"""
response = get_completion(prompt)
print(response)
```

    Dear Sir/Madam,
    
    I am writing to bring to your attention a standing lamp that I believe may be of interest to you. Please find attached the specifications for your review.
    
    Thank you for your time and consideration.
    
    Sincerely,
    
    Joe
    

## Format Conversion
ChatGPT can translate between formats. The prompt should describe the input and output formats.


```python
data_json = { "resturant employees" :[ 
    {"name":"Shyam", "email":"shyamjaiswal@gmail.com"},
    {"name":"Bob", "email":"bob32@gmail.com"},
    {"name":"Jai", "email":"jai87@gmail.com"}
]}

prompt = f"""
Translate the following python dictionary from JSON to an HTML \
table with column headers and title: {data_json}
"""
response = get_completion(prompt)
print(response)
```

    <table>
      <caption>Restaurant Employees</caption>
      <thead>
        <tr>
          <th>Name</th>
          <th>Email</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Shyam</td>
          <td>shyamjaiswal@gmail.com</td>
        </tr>
        <tr>
          <td>Bob</td>
          <td>bob32@gmail.com</td>
        </tr>
        <tr>
          <td>Jai</td>
          <td>jai87@gmail.com</td>
        </tr>
      </tbody>
    </table>
    


```python
from IPython.display import display, Markdown, Latex, HTML, JSON
display(HTML(response))
```


<table>
  <caption>Restaurant Employees</caption>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Shyam</td>
      <td>shyamjaiswal@gmail.com</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>bob32@gmail.com</td>
    </tr>
    <tr>
      <td>Jai</td>
      <td>jai87@gmail.com</td>
    </tr>
  </tbody>
</table>


## Spellcheck/Grammar check.

Here are some examples of common grammar and spelling problems and the LLM's response. 

To signal to the LLM that you want it to proofread your text, you instruct the model to 'proofread' or 'proofread and correct'.


```python
text = [ 
  "The girl with the black and white puppies have a ball.",  # The girl has a ball.
  "Yolanda has her notebook.", # ok
  "Its going to be a long day. Does the car need it’s oil changed?",  # Homonyms
  "Their goes my freedom. There going to bring they’re suitcases.",  # Homonyms
  "Your going to need you’re notebook.",  # Homonyms
  "That medicine effects my ability to sleep. Have you heard of the butterfly affect?", # Homonyms
  "This phrase is to cherck chatGPT for speling abilitty"  # spelling
]
for t in text:
    prompt = f"""Proofread and correct the following text
    and rewrite the corrected version. If you don't find
    and errors, just say "No errors found". Don't use 
    any punctuation around the text:
    ```{t}```"""
    response = get_completion(prompt)
    print(response)
```

    The girl with the black and white puppies has a ball.
    No errors found.
    It's going to be a long day. Does the car need its oil changed?
    Their goes my freedom. There going to bring they're suitcases.
    
    Corrected version: 
    There goes my freedom. They're going to bring their suitcases.
    You're going to need your notebook.
    That medicine affects my ability to sleep. Have you heard of the butterfly effect?
    This phrase is to check ChatGPT for spelling ability.
    


```python
text = f"""
Got this for my daughter for her birthday cuz she keeps taking \
mine from my room.  Yes, adults also like pandas too.  She takes \
it everywhere with her, and it's super soft and cute.  One of the \
ears is a bit lower than the other, and I don't think that was \
designed to be asymmetrical. It's a bit small for what I paid for it \
though. I think there might be other options that are bigger for \
the same price.  It arrived a day earlier than expected, so I got \
to play with it myself before I gave it to my daughter.
"""
prompt = f"proofread and correct this review: ```{text}```"
response = get_completion(prompt)
print(response)
```

    I got this for my daughter's birthday because she keeps taking mine from my room. Yes, adults also like pandas too. She takes it everywhere with her, and it's super soft and cute. However, one of the ears is a bit lower than the other, and I don't think that was designed to be asymmetrical. Additionally, it's a bit small for what I paid for it. I think there might be other options that are bigger for the same price. On the positive side, it arrived a day earlier than expected, so I got to play with it myself before I gave it to my daughter.
    


```python
from redlines import Redlines

diff = Redlines(text,response)
display(Markdown(diff.output_markdown))
```


<span style="color:red;font-weight:700;text-decoration:line-through;">Got </span><span style="color:red;font-weight:700;">I got </span>this for my <span style="color:red;font-weight:700;text-decoration:line-through;">daughter for her </span><span style="color:red;font-weight:700;">daughter's </span>birthday <span style="color:red;font-weight:700;text-decoration:line-through;">cuz </span><span style="color:red;font-weight:700;">because </span>she keeps taking mine from my <span style="color:red;font-weight:700;text-decoration:line-through;">room.  </span><span style="color:red;font-weight:700;">room. </span>Yes, adults also like pandas <span style="color:red;font-weight:700;text-decoration:line-through;">too.  </span><span style="color:red;font-weight:700;">too. </span>She takes it everywhere with her, and it's super soft and <span style="color:red;font-weight:700;text-decoration:line-through;">cute.  One </span><span style="color:red;font-weight:700;">cute. However, one </span>of the ears is a bit lower than the other, and I don't think that was designed to be asymmetrical. <span style="color:red;font-weight:700;text-decoration:line-through;">It's </span><span style="color:red;font-weight:700;">Additionally, it's </span>a bit small for what I paid for <span style="color:red;font-weight:700;text-decoration:line-through;">it though. </span><span style="color:red;font-weight:700;">it. </span>I think there might be other options that are bigger for the same <span style="color:red;font-weight:700;text-decoration:line-through;">price.  It </span><span style="color:red;font-weight:700;">price. On the positive side, it </span>arrived a day earlier than expected, so I got to play with it myself before I gave it to my daughter.



```python
prompt = f"""
proofread and correct this review. Make it more compelling. 
Ensure it follows APA style guide and targets an advanced reader. 
Output in markdown format.
Text: ```{text}```
"""
response = get_completion(prompt)
display(Markdown(response))
```


Title: A Soft and Cute Panda Plushie for All Ages

As an adult, I can attest that pandas are not just for kids. That's why I got this adorable panda plushie for my daughter's birthday, after she kept taking mine from my room. And let me tell you, it was a hit!

The plushie is super soft and cuddly, making it the perfect companion for my daughter. She takes it everywhere with her, and it has quickly become her favorite toy. However, I did notice that one of the ears is a bit lower than the other, which I don't think was designed to be asymmetrical. But that doesn't take away from its cuteness.

The only downside is that it's a bit small for the price I paid. I think there might be other options that are bigger for the same price. But overall, I'm happy with my purchase.

One thing that surprised me was that it arrived a day earlier than expected. This gave me the chance to play with it myself before giving it to my daughter. And I have to say, I was impressed with the quality and attention to detail.

In conclusion, if you're looking for a soft and cute panda plushie for yourself or a loved one, this is definitely a great option. Just be aware that it might be a bit smaller than expected.


Thanks to the following sites:

https://writingprompts.com/bad-grammar-examples/

