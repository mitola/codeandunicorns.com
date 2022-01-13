---
widget: blank
headless: true

# ... Put Your Section Options Here (title etc.) ...
title: Subscribe to newsletter
subtitle:
weight: 250  # section position on page
design:
  # Choose how many columns the section has. Valid values: 1 or 2.
  columns: '2'
---
<form name="subscribe" method="POST" data-netlify="true">
  <p>
    <label>Your Email: <input type="email" name="email" class="form-control w-100"/></label>
  </p>
  <p>
    <button type="submit" class="btn btn-primary px-3 py-2 w-20">Send</button>
  </p>
</form>