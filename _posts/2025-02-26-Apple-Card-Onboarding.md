---
title: "Apple Card Onboarding UX: a Review"
date: 2025-02-26
header:
  teaser: /assets/images/apple-card/teaser@2x.jpg
categories:
  - Blog
tags:
  - Tech
  - Design
gallery1:
  - url: /assets/images/apple-card/03.jpg
    image_path: /assets/images/apple-card/03.jpg
    alt: "image"
  - url: /assets/images/apple-card/04.jpg
    image_path: /assets/images/apple-card/04.jpg
    alt: "image"
gallery2:
  - url: /assets/images/apple-card/11.jpg
    image_path: /assets/images/apple-card/11.jpg
    alt: "image"
  - url: /assets/images/apple-card/12.jpg
    image_path: /assets/images/apple-card/12.jpg
    alt: "image"
  - url: /assets/images/apple-card/13.jpg
    image_path: /assets/images/apple-card/13.jpg
    alt: "image"
gallery3:
  - url: /assets/images/apple-card/14.jpg
    image_path: /assets/images/apple-card/14.jpg
    alt: "image"
  - url: /assets/images/apple-card/15.jpg
    image_path: /assets/images/apple-card/15.jpg
    alt: "image"
gallery4:
  - url: /assets/images/apple-card/18.jpg
    image_path: /assets/images/apple-card/18.jpg
    alt: "image"
  - url: /assets/images/apple-card/19.jpg
    image_path: /assets/images/apple-card/19.jpg
    alt: "image"
  - url: /assets/images/apple-card/20.jpg
    image_path: /assets/images/apple-card/20.jpg
    alt: "image"
gallery5:
  - url: /assets/images/apple-card/23.jpg
    image_path: /assets/images/apple-card/23.jpg
    alt: "image"
  - url: /assets/images/apple-card/24.jpg
    image_path: /assets/images/apple-card/24.jpg
    alt: "image"
  - url: /assets/images/apple-card/25.jpg
    image_path: /assets/images/apple-card/25.jpg
    alt: "image"
gallery6:
  - url: /assets/images/apple-card/27.jpg
    image_path: /assets/images/apple-card/27.jpg
    alt: "image"
  - url: /assets/images/apple-card/28.jpg
    image_path: /assets/images/apple-card/28.jpg
    alt: "image"
gallery7:
  - url: /assets/images/apple-card/30.jpg
    image_path: /assets/images/apple-card/30.jpg
    alt: "image"
  - url: /assets/images/apple-card/31.jpg
    image_path: /assets/images/apple-card/31.jpg
    alt: "image"
  - url: /assets/images/apple-card/32.jpg
    image_path: /assets/images/apple-card/32.jpg
    alt: "image"
---

This my most popular article on [Medium](https://medium.com/@pe8er/apple-card-onboarding-ux-a-review-cacffe55af95) (23K views, 8.6K reads, wow!), originally written in August 2019. I'm importing it here in an effort to take ownership of my own words.
{: .notice}

![Apple Card Teaser](/assets/images/apple-card/teaser@2x.jpg)

This is an analysis of the new Apple Card onboarding experience in iOS Wallet app.<!--more--> I wrote it primarily for my work colleagues, since at [Persistent](http://www.persistent.com) we do a lot of stuff with financial industry and all our customers are trying to be digital. I thought it may [find its worth in the waking world](https://www.youtube.com/watch?v=Wgxkbq8Wya4), so I’m dropping it on Medium instead of making an internal presentation.<!--more-->


## Context

The financial industry is on a bumpy journey of transforming itself to stay relevant in increasingly digital lifestyles. It means focus on human, which is a bit of a foreign concept for the financial industry. It means understanding the new generation, their behaviors and expectations. It means creating products which are useful, compelling and truly add value to human life. It sounds like a job for product and UX people, so why not take inspiration from a company that built its success on attention to user experience?

I think Apple Card’s design, the thinking behind it as well as world’s reaction and adoption deserve to be studied. Apple is about to disrupt the personal finance industry by simply following their principles:

* Emphasis on simplicity and tasteful visual discipline
* Fine-tuned information hierarchy and density
* A pinch of emphasis on privacy and user control
* Excellent perceived performance

Apple’s product design goals are very different from the rest of the industry, and they are usually able to deliver on those. So let’s dig right into it and see if it’s a good inspiration for onboarding UX in personal finance context.

I requested to be notified about Apple Card availability a while ago. I never received a push notification or email. So I was surprised that after updating to iOS 13 beta 7, I randomly opened the Wallet app and there it was:

![](/assets/images/apple-card/01.jpg)

It was impossible to not notice and focused on getting me to apply. I appreciate the boldness, but it made an assumption I already knew what Apple Card was. Unless it was driven by the fact that I had expressed interest in it earlier, it may be a risky idea to not explain the offered product at all.

Luckily I’m *the right* *target audience*, so I tapped **Apply**. Next up:

![](/assets/images/apple-card/02.jpg)

A modal view slides in and we’re presented with a funny and sad reality of UX design in 2019: privacy and legal statements fight for screen real estate with marketing and UI controls. Let’s tap **Continue:**

{% include gallery id="gallery1" %}

All fields were pre-filled automatically from my iCloud account information. Keyboard did not appear automatically. Visually it’s quite dry, but UX focuses on speed and efficiency.

One somewhat significant omission: UI doesn’t set any expectations with regards to how many or what steps I would have to go through to complete the process. It’s always a good idea to inform the user that setup involves “just 4 steps” and should take “no more than 3 minutes”. And then UI should clearly indicate what is the current step, which steps are complete and which are incomplete. No surprises equals less impatience, frustration and anxiety.

Okay, upon tapping the tiny and very-far-from-my-thumb **Next** button (seriously Apple? Nav bars need to go away), comes the important question:

![](/assets/images/apple-card/05.jpg)

Keyboard slid in automatically and SSN was not pre-filled. I appreciate the fact UI is asking me for just the last 4 digits, as I’m more likely to remember those rather than the entire number.

Interestingly, the app doesn’t know I’m not a US citizen. Could it know that though? I’ve no idea where would it know from, aside from making assumptions based on my data.

**Next** button is disabled until I enter my SSN. Once submitted, here’s how UI reacts:

![](/assets/images/apple-card/06.jpg)

I think this is somewhat lazy. I like the fact that response and progress are shown within the same screen and I understand what they are going for here. *UI tells me it is in progress of verifying my identity*. I think it could be optimized though:

* It would be better if progress indicator was definite (i.e. a progress bar instead of an infinite spinner) — that would set a clear expectation of what is going on now and at what rate is it progressing towards completion.
* Progress indicator should be visually associated with either the data I entered (it is verifying my SSN and citizenship after all) or with the button I just pressed and I’m still looking at (so replace **Next** with it).

Next up, the app wants to make sure I can afford the card:

![](/assets/images/apple-card/07.jpg)

Fair enough. “Required” indicator as placeholder text is quite elegant. Upon entering the number, it seems like the app has everything it needs because it shows me the agreement:

![](/assets/images/apple-card/08.jpg)

Ignore the empty, pointless nav bar. I wonder if Apple had any say in how and what information is presented here, because it is quite *digestible*. As an immigrant who had struggled to understand communications from US financial institutions in the past, I was positively surprised:

![](/assets/images/apple-card/09.jpg)

I obviously agreed and the UI let me know it was on it:

![](/assets/images/apple-card/10.jpg)

However, it ran into a problem which it didn’t explain very well.

{% include gallery id="gallery2" %}

I totally forgot I had frozen my credit! UI didn’t bother to explain it until after it tried again with my full SSN and failed. A small explanation of why all of a sudden I’m asked for my full SSN would be appreciated.

Another problem: the final (rightmost above) screen left me with no options other than to admit defeat. It could try a little harder, offer a way to contact TransUnion and put itself *on hold *while I would be lifting the freeze. By the way it is totally possible, as evidenced by the email I received as soon as SSN verification failed:

{% include gallery id="gallery3" caption="Toolbar in the new Mail app sucks." %}

There is a very clear call to action with TransUnion contact information, but it’s at the *bottom* of the email. Had I not been curious, I would have never seen it.

And another nitpick: why not provide a URL to TransUnion website and encourage me to sort it out in a way that is more convenient than a phone call? Maybe Apple designers don’t believe credit agency websites work properly? I was skeptical too but I was delighted to be proven completely wrong:

![](/assets/images/apple-card/16.jpg)
<figcaption>Damn, this took less than a minute and nothing exploded in the process. Impressive.</figcaption>

For a truly seamless experience, Apple could integrate with all credit bureaus right within the Wallet app. I’m sure an agreement and a simple authentication + temporary credit unfreeze API could be worked out.

Anyway. With my credit temporarily unfrozen, I came back to Wallet app, subconsciously expecting that it had saved my application progress. Well, I was wrong:

![](/assets/images/apple-card/17.jpg)
<figcaption>This app has amnesia, man!</figcaption>

That’s not great. Long story short — I went through all setup screens again and after accepting the agreement, something new happened:

{% include gallery id="gallery4" caption="By the way, it asked me for the back of my ID too." %}

Alright! I don’t love the ID illustration — it looks like a *template *of an illustration, but it gets the job done. On the other hand photo capture is done very well — camera viewport is similar in size and aspect ratio to dimensions of an ID, which is a great guide. And best of all, camera recognized my ID automatically, I didn’t have to press the shutter button at all. Very cool.

One silly thing — why is the destructive “Scan Again” action closer to my thumb than the button Apple wants me to press? And while we’re at it, wouldn’t it make more sense to place it in the nav bar or immediately below the captured photo?

And then on the last screen, where almost all text and UI controls faded out, it would be cool if photo of my ID flew up towards the illustration and replaced it. That would further reinforce the communication that my ID is being submitted, uploaded and accepted.

One last nitpick: what happens if I tap **Cancel**? ****Does it cancel the photo ID verification, or the entire application? I think clearer navigation and communication as to where the user is in setup process could go a long way in eliminating small confusions like this.

Not to mention how placement of ID illustration, big title and **Continue **button is inconsistent between the screens.

Onto the next step:

![](/assets/images/apple-card/21.jpg)
<figcaption>Head of Wallet app design team: guys, please create the LEAST exciting and COLDEST representation of success you can imagine.</figcaption>

Okay, this is happening! I appreciated that it was *my decision** ***and loved the language. “Accept” verb is empowering. The three data points — credit limit, APR and fees are powerful and chosen wisely too. But overall, this UI couldn’t be colder. I think a successful passing through the maze of entering personal information, credit score checks and ID verification deserves a bit of fanfare. Come on Apple, give us confetti!

I’m a total fanboy though so I didn’t care and hit that **Accept** button, hard. And I had to squint:

![](/assets/images/apple-card/22.jpg)

If you’re not paying attention, this UI says “leave me alone, I’m not sure what’s next”. It’s quite scary actually. Disabling the big fat colorful button I just pressed (with a lot of joy) is not very reassuring. It took me a second to notice the tiny, indefinite progress indicator up top. I did not enjoy this moment at all.

But thankfully it worked and next up, Apple pushed their agenda:

{% include gallery id="gallery5" %}

Fair enough. I understand this question is supposed to be a compromise between:

* setting it as default automatically (bad: don’t change user’s settings without consent) and
* not setting it as default at all (bad for Apple, they obviously want all your transactions to go through their card).

But I can’t help but wonder — does this need to be a big, 3-state full screen flow? What if it was a toggle on the previous screen? Like so:

![](/assets/images/apple-card/26.jpg)

Why not? Anyway, next up: plastic, I mean titanium.

{% include gallery id="gallery6" %}

I love how it shows me *my* *card* and I’m wondering if this idea could be carried over to the previous setup steps, where the card illustration was a somewhat useless visual adornment.

The **No Thanks** action is a funny conundrum — grammar is weird (shouldn’t it be “No, thanks”?) and then the positioning: it is risky, but it sets a precedent (exit action is at the bottom of the screen) only to immediately contradict it on the following screen, where **Cancel **button moves to the nav bar.

And finally, the setup is complete and this is what “Day zero” UI looks like:

![](/assets/images/apple-card/29.jpg)

It serves the purpose, but it’s not particularly engaging or interesting. Having seen all the cool mockups on Apple website, I was somewhat disappointed when presented with this sea of black, grey and white.

Is there a reason why the “i” icon is so disproportionally large? Why is it trying to draw my attention so hard now, when I don’t have any upcoming payments and I frankly don’t care about them?

Maybe they could consider a contextual tutorial with placeholder data instead of these empty screens? Same thing when I drill down. This is a missed opportunity to explain the specifics and differentiators:

{% include gallery id="gallery7" caption="Layouts look pretty great but I have to spend some money to see how they behave in action." %}

## All done!

Here we are then: the setup is complete, I can start using the card immediately (very cool) and the physical titanium will be in my mailbox in 5–7 days (that’s a bit long but whatever — *damn, look at the first world problem of this Amazon Prime member*). Overall I appreciate that experience was focused and quite smooth, the UI actively helped me push past the boring and annoying steps. My two largest criticisms are **amnesia** and **cold shoulder**: it didn’t remember my progress when I had to unfreeze my credit and it did not congratulate me in any meaningful way at the end of the setup.

I imagine most people may not notice some of the bumps I discovered, but I believe there is room for Apple to iron out the kinks and make the experience more colorful and exciting. Observable lack of attention to detail is unfortunate, but not surprising. Apple is pretty great at UX design but not flawless.

But still: even in the current state the flow, information density, behavior and visuals are miles ahead of industry standards. I’m looking forward to seeing major financial institutions being forced to follow Apple’s example and start morphing their offerings into things that normal people can relate to and be excited about. Fintech startups have started the revolution a while ago, but Apple is *the behemoth* that drives a huge amount of urgency just by their size and influence on the public.

There is no turning back now!