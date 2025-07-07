# **How to Integrate Freshdesk Ticketing with a Custom Contact Form (Using JavaScript + API)**

When customers reach out for help, the experience they have in that moment matters more than anything else. If you're using Freshdesk to manage support, that's a smart move. But what if you want a **custom support form** on your website—one that matches your brand, layout, and user flow?

In this guide, I’ll show you **how to build your own HTML contact form** and use **JavaScript** to send data directly to **Freshdesk via API**. No plugins. No frameworks. Just clean, customizable code that gives you full control.

Let’s dive in.

---

## **Why Use a Custom Contact Form Instead of Freshdesk’s Widget?**

Freshdesk already provides a default contact widget—but it may not always fit your design.

Here’s why developers often go custom:

* You want the form to **match your branding**
* You need it inside a custom modal, popup, or page section
* You want to **extend logic** like pre-fill values, file upload, or product-specific tags
* You want to reduce third-party scripts for speed

Whatever the reason—custom forms give you **freedom**.

---

## **What You’ll Need Before Starting**

* A **Freshdesk account**
* Your **Freshdesk API Key**
* Your **Freshdesk domain (e.g., yourcompany.freshdesk.com)**
* A basic HTML file (or access to your web project)
* A little patience, just for the setup part 🙂

---

## **Step 1: Get Your Freshdesk API Key**

1. Log into your Freshdesk account
2. Click your profile icon → **Profile Settings**
3. You’ll see your **API key** on that page—copy it

You’ll need this for authorization when making API requests.

---

## **Step 2: Create Your HTML Contact Form**

Let’s start by writing a simple HTML form:

```html
<form id="supportForm">
  <h2>Need Help? Submit a Ticket</h2>
  <input type="text" name="name" placeholder="Your Name" required />
  <input type="email" name="email" placeholder="Your Email" required />
  <textarea name="message" placeholder="Describe your issue..." required></textarea>
  <button type="submit">Submit Ticket</button>
</form>

<div id="responseMsg"></div>
```

Keep it simple. This is your base. You can style it later with CSS or a framework like Tailwind.

---

## **Step 3: Add JavaScript to Handle Form Submission**

Now we’ll use **vanilla JavaScript** to:

1. Get form data
2. Send it to Freshdesk API
3. Show success or failure messages

Here’s the complete JS code:

```html
<script>
  document.getElementById('supportForm').addEventListener('submit', async function(e) {
    e.preventDefault();

    const name = e.target.name.value;
    const email = e.target.email.value;
    const message = e.target.message.value;

    const data = {
      name: name,
      email: email,
      description: message,
      status: 2, // 2 = Open
      priority: 1 // 1 = Low, 2 = Medium, 3 = High, 4 = Urgent
    };

    try {
      const response = await fetch('https://YOURDOMAIN.freshdesk.com/api/v2/tickets', {
        method: 'POST',
        headers: {
          'Authorization': 'Basic ' + btoa('YOUR_API_KEY:X'),
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
      });

      const resultDiv = document.getElementById('responseMsg');

      if (response.ok) {
        resultDiv.innerText = "Your ticket has been submitted successfully!";
        e.target.reset();
      } else {
        const error = await response.json();
        resultDiv.innerText = "Something went wrong: " + (error.message || "Unknown error");
      }

    } catch (err) {
      document.getElementById('responseMsg').innerText = "Network error. Please try again.";
    }
  });
</script>
```

---

## **Important Notes on Authorization**

Freshdesk uses **Basic Authentication**, where the API key is encoded using `btoa('API_KEY:X')`.

⚠️ **Never expose this key in public frontend code** if your form is client-side only.
For production, always **proxy this request through a backend** that hides your API key.

---

## **Bonus: Add Tags or Custom Fields**

Want to include extra data like tags or custom fields? Freshdesk supports them via API:

```js
const data = {
  name: "John Doe",
  email: "john@example.com",
  description: "Problem with login",
  priority: 1,
  status: 2,
  tags: ["login", "frontend-form"],
  custom_fields: {
    cf_browser: "Chrome"
  }
};
```

Replace `cf_browser` with the field key set up in your Freshdesk ticket fields.

---

## **Improving the UX with a Loader**

You can add a loader or disable the submit button while the form is sending:

```js
const submitBtn = e.target.querySelector('button');
submitBtn.disabled = true;
submitBtn.innerText = "Submitting...";
```

Then revert it after the response:

```js
submitBtn.disabled = false;
submitBtn.innerText = "Submit Ticket";
```

Small touches like this make a huge difference.

---

## **Common Errors and How to Fix Them**

| **Issue**                | **Fix**                                                                       |
| ------------------------ | ----------------------------------------------------------------------------- |
| 403 Forbidden            | Check API key and domain. Make sure you're using correct headers.             |
| 422 Unprocessable Entity | One or more fields are missing or invalid. Check `description`, `email`, etc. |
| Network Error / CORS     | Don't call Freshdesk API directly from frontend in production. Use a server.  |

---

## **Final Thoughts – Own the Support Experience**

With just a bit of HTML and JavaScript, you've now created a **custom, brand-friendly contact form** that talks directly to Freshdesk.

This is more than just code. You’ve just built a better customer experience. One that feels native. One that you control. One that tells your users: *“We care.”*

Now you can:

* Integrate this into your product
* Extend it with file uploads, drop-downs, or auto-tagging
* Add backend proxying for better security

And most importantly, you can grow without losing that personal touch.
