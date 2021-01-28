---
title: "3. Bitcoin Bot"
date: 2021-01-28T12:22:48-07:00
draft: false
---

Thursday, January 28th, 2021

Like many others, I have had friends telling me to buy bitcoin. These are the same friends that told me to buy $GME and $AMC. However, I couldn't resist throwing a few thousand dollars of schmuck insurance Bitcoin's way. Because I'm typically a long term investor, I've never seen the need to constantly monitor the price of an asset in which I've invested. But, now that I've become a Bitcoin trader, I thought it might be cool to somehow update myself anytime Bitcoin reaches a significant benchmark. 

Here's a link to the github for the code:
<a href='https://github.com/hallwyatt3/Bitcoin-Discord-Bot'>GitHub Repo</a>

I think another reason I wanted to build this is because I wanted to build a Discord Webhook...

First, I had to spend some time figuring out how to find a real time updated API for the price of Bitcoin. I tried a few different sources that would only update every minute, but I wanted real time. Luckily, Coinbase Pro's API is available free for anyone to use. 

<pre><code>
def price():
    price = requests.get('https://api.pro.coinbase.com/products/BTC-USD/ticker')
    rate = price.json()['price']
    return float(rate)
</code></pre><br>

Second, I had to figure out how to push my message to the Webhook. As expected, Discord makes it super easy and I really didn't have to figure out much.

<pre><code>
def package_message(self,message):
    webhook = DiscordWebhook(url=webhook_url, content=message)
    response = webhook.execute()
</code></pre><br>

Third, I had to define a "significant event". I decided that every time it crossed a $1 thousand threshold counts as a significant event. (I tried $100 at first but it was pushing a message every time it called the API because the price moves so quickly.) To do this, I had to check the price initially and save that to a variable called price. Then I checked the price a second time to see if the threshold had been crossed. To do this I rounded both numbers to the thousands place (e.g. $32,459 becomes $32,000) and checked if they were equal. If not, the threshold has been crossed and a message is sent to Discord. Then the second price is saved as the first price and the loop repeats. 

<pre><code>
def bitcoin_significant(self):
    global rate
    sleep(60)
    rate2 = price()
    #if rate and current price() do not equal each other when rounded to the 
    #nearest thousand, that means the number has crossed a 
    #threshold
    print(f'Rate-1 is {rate} and rate-2 is {rate2}', end='\r')
    if self.round_to_thousand(rate) != self.round_to_thousand(rate2):
        print(f'Message sent to Discord Chat. ${rate}')
        rate = rate2
        return True
    else:
        rate = rate2
</code></pre><br>

Finally, the messages that were being pushed only mentioned the price, not the threshold that had been crossed. So, I added "$XX,000 threshold has been crossed" to each message.

I don't want to get involved in a debate about the future of Bitcoin. Like I said, this was just for a few dollars of schmuck insurance but at the end of the day, I've learned how to call an API and push messages to Discord. 

Anyways, the 2% fees for trading Bitcoin is ridiculous. 