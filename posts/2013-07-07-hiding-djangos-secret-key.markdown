---
title: Hiding Django's Secret Key
author: Miles Steele
---

Django uses a [secret key](https://docs.djangoproject.com/en/1.5/ref/settings/#std:setting-SECRET_KEY) for many of its security features. This secret key should **not** be checked into version control. There are many ways to factor out the secret key; here's the one I use.

The secret key entry is stored by default in `settings.py`:
``` python
# Make this unique, and don't share it with anybody.
SECRET_KEY = 'y2k94mib_ve%c9hth=9grurdontuse1(t&his;jy-xkcd'
```

From the [Django Docs](https://docs.djangoproject.com/en/1.5/ref/settings/#std:setting-SECRET_KEY):

> Running Django with a known SECRET_KEY defeats many of Django’s security protections, and can lead to privilege escalation and remote code execution vulnerabilities.

In your `settings.py`, replace the `SECRET_KEY` entry with the following block of code. It will look for an existing secret key in and if it does not find one, then it will generate and save one into `secret_key.py` when the settings file is used.
``` python
def find_or_create_secret_key():
    """
    Look for secret_key.py and return the SECRET_KEY entry in it if the file exists.
    Otherwise, generate a new secret key, save it in secret_key.py, and return the key.
    """
    SECRET_KEY_FILEPATH = os.path.join(os.path.dirname(__file__), 'secret_key.py')

    if os.path.isfile(secret_key_filepath):
        from secret_key import SECRET_KEY
        return SECRET_KEY
    else:
        from django.utils.crypto import get_random_string
        chars = 'abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&amp;*(-_=+)'
        new_key = get_random_string(50, chars)
        with file(SECRET_KEY_FILEPATH, 'w') as f:
            f.write("# Django secret key\n# Do NOT check this into version control.\n\nSECRET_KEY = '%s'\n" % new_key)
        from secret_key import SECRET_KEY
        return SECRET_KEY

# Make this unique, and don't share it with anybody.
SECRET_KEY = find_or_create_secret_key()
```

That's it. Be sure to add `secret_key.py` to your `.gitignore`!
