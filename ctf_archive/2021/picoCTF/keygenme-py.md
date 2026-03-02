# keygenme-py
Category: Reverse Engineering, 30 points

## Description

A Python file was attached.

```python
#============================================================================#
#============================ARCANE CALCULATOR===============================#
#============================================================================#

import hashlib
from cryptography.fernet import Fernet
import base64



# GLOBALS --v
arcane_loop_trial = True
jump_into_full = False
full_version_code = ""

username_trial = "GOUGH"
bUsername_trial = b"GOUGH"

key_part_static1_trial = "picoCTF{1n_7h3_|<3y_of_"
key_part_dynamic1_trial = "xxxxxxxx"
key_part_static2_trial = "}"
key_full_template_trial = key_part_static1_trial + key_part_dynamic1_trial + key_part_static2_trial

star_db_trial = {
  "Alpha Centauri": 4.38,
  "Barnard's Star": 5.95,
  "Luhman 16": 6.57,
  "WISE 0855-0714": 7.17,
  "Wolf 359": 7.78,
  "Lalande 21185": 8.29,
  "UV Ceti": 8.58,
  "Sirius": 8.59,
  "Ross 154": 9.69,
  "Yin Sector CL-Y d127": 9.86,
  "Duamta": 9.88,
  "Ross 248": 10.37,
  "WISE 1506+7027": 10.52,
  "Epsilon Eridani": 10.52,
  "Lacaille 9352": 10.69,
  "Ross 128": 10.94,
  "EZ Aquarii": 11.10,
  "61 Cygni": 11.37,
  "Procyon": 11.41,
  "Struve 2398": 11.64,
  "Groombridge 34": 11.73,
  "Epsilon Indi": 11.80,
  "SPF-LF 1": 11.82,
  "Tau Ceti": 11.94,
  "YZ Ceti": 12.07,
  "WISE 0350-5658": 12.09,
  "Luyten's Star": 12.39,
  "Teegarden's Star": 12.43,
  "Kapteyn's Star": 12.76,
  "Talta": 12.83,
  "Lacaille 8760": 12.88
}


def intro_trial():
    print("\n===============================================\n\
Welcome to the Arcane Calculator, " + username_trial + "!\n")    
    print("This is the trial version of Arcane Calculator.")
    print("The full version may be purchased in person near\n\
the galactic center of the Milky Way galaxy. \n\
Available while supplies last!\n\
=====================================================\n\n")


def menu_trial():
    print("___Arcane Calculator___\n\n\
Menu:\n\
(a) Estimate Astral Projection Mana Burn\n\
(b) [LOCKED] Estimate Astral Slingshot Approach Vector\n\
(c) Enter License Key\n\
(d) Exit Arcane Calculator")

    choice = input("What would you like to do, "+ username_trial +" (a/b/c/d)? ")
    
    if not validate_choice(choice):
        print("\n\nInvalid choice!\n\n")
        return
    
    if choice == "a":
        estimate_burn()
    elif choice == "b":
        locked_estimate_vector()
    elif choice == "c":
        enter_license()
    elif choice == "d":
        global arcane_loop_trial
        arcane_loop_trial = False
        print("Bye!")
    else:
        print("That choice is not valid. Please enter a single, valid \
lowercase letter choice (a/b/c/d).")


def validate_choice(menu_choice):
    if menu_choice == "a" or \
       menu_choice == "b" or \
       menu_choice == "c" or \
       menu_choice == "d":
        return True
    else:
        return False


def estimate_burn():
  print("\n\nSOL is detected as your nearest star.")
  target_system = input("To which system do you want to travel? ")

  if target_system in star_db_trial:
      ly = star_db_trial[target_system]
      mana_cost_low = ly**2
      mana_cost_high = ly**3
      print("\n"+ target_system +" will cost between "+ str(mana_cost_low) \
+" and "+ str(mana_cost_high) +" stone(s) to project to\n\n")
  else:
      # TODO : could add option to list known stars
      print("\nStar not found.\n\n")


def locked_estimate_vector():
    print("\n\nYou must buy the full version of this software to use this \
feature!\n\n")


def enter_license():
    user_key = input("\nEnter your license key: ")
    user_key = user_key.strip()

    global bUsername_trial
    
    if check_key(user_key, bUsername_trial):
        decrypt_full_version(user_key)
    else:
        print("\nKey is NOT VALID. Check your data entry.\n\n")


def check_key(key, username_trial):

    global key_full_template_trial

    if len(key) != len(key_full_template_trial):
        return False
    else:
        # Check static base key part --v
        i = 0
        for c in key_part_static1_trial:
            if key[i] != c:
                return False

            i += 1

        # TODO : test performance on toolbox container
        # Check dynamic part --v
        if key[i] != hashlib.sha256(username_trial).hexdigest()[4]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[5]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[3]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[6]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[2]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[7]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[1]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[8]:
            return False



        return True


def decrypt_full_version(key_str):

    key_base64 = base64.b64encode(key_str.encode())
    f = Fernet(key_base64)

    try:
        with open("keygenme.py", "w") as fout:
          global full_version
          global full_version_code
          full_version_code = f.decrypt(full_version)
          fout.write(full_version_code.decode())
          global arcane_loop_trial
          arcane_loop_trial = False
          global jump_into_full
          jump_into_full = True
          print("\nFull version written to 'keygenme.py'.\n\n"+ \
                 "Exiting trial version...")
    except FileExistsError:
    	sys.stderr.write("Full version of keygenme NOT written to disk, "+ \
	                  "ERROR: 'keygenme.py' file already exists.\n\n"+ \
			  "ADVICE: If this existing file is not valid, "+ \
			  "you may try deleting it and entering the "+ \
			  "license key again. Good luck")

def ui_flow():
    intro_trial()
    while arcane_loop_trial:
        menu_trial()



# Encrypted blob of full version
full_version = \
b"""
gAAAAABgT_[AWS_SECRET_REMOVED]N2hYYw-[AWS_SECRET_REMOVED]q-l6PpL0Fzzjo1x_E2Jjbw_[AWS_SECRET_REMOVED]etxFtmYiHLMB0_kqueeT8zf_vcXAPzbiYA0hvD_QSAXzPiKwM2IsGpGzIS5O4_ODq6-knKszeQFstWKFNH_-jNAylCTWSQpPrWqJxCWhSINPhOZ9-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]KiE_6BrRMHZW01hcYbfNa1TdJ1MLUX64e_tpDDjMfKvlXZ1qMx4GDwR2lFza9_fm98zoaV-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]o15FS-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]y9V1LN2Furmz_[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8MHo5DcnVvH0yPlnc9Zn2s3_[AWS_SECRET_REMOVED]5yf_ybd9Tcx_u6xLtwUZBERZWt931n4hQN8n4C_XmDsMehuoSuFmi2NpAuDhX2rcEQK86Ito0KYp-[AWS_SECRET_REMOVED]F5rZtxtNz3Wsm_j4tqL4Tom27YE5eK7LQSi5B-AsmSF5JGTam2mWeykOGyE-3pHZmNxxkRfdRjxM0uFV13yjQSLFgNIkZ--[AWS_SECRET_REMOVED]KOUieV6VbPJdSL0Sf1uGDmbRFdMmopDm-AuS-7-MLBGiOPmwXtse_[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]x_dLkUdomAPMPY0cRoreYsXmFKkEWhYg-[AWS_SECRET_REMOVED]o3n8_[AWS_SECRET_REMOVED]cK4oFVpLGwOrhJdQhJWVqxQdt0ULS-ROB08eOglsXifnVrDl0hi2B0EYcWxxGs-[AWS_SECRET_REMOVED]NiL5sfL2mhakMI8XGcljZw2KI0ldgaOW_UvAgh8N3FgKUR4qh_iJ0raoQaaJJFbFneKRoDNT3QsywP_[AWS_SECRET_REMOVED]aexAKjpYGphE7K4e1g3fHWYjvgnJ-AoXfqALOwDLzaLRjVHSsgF1TQl39XgiAgEzJL-7w_zBn_Hxl5BfYtqe4vxf4PVMZGvof0jXMpM4W-AQ8IW-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]_ZOoa2__iAj3VlQjcrQynzrxoT5NASs_[AWS_SECRET_REMOVED]tJfATgYue7xc1BotMDO0Uj5q-wfcg03dq1cZmJ_[AWS_SECRET_REMOVED]FjlqasFZ4jSuNcqybxS3ocP4z-[AWS_SECRET_REMOVED]bTcj7WXWj9WNqxcrF_swHNeIkOPByEa34TIXyJvEOGefZOw7h-F-hxCGuho7LOwabIopS6IykeSLMw1Ra4COmYN-VamVHVUGo50AVEXcmcnHL9NmXP_812Y_sSFdNPo-[AWS_SECRET_REMOVED]c5Tm5BZ42dfJuYZeTwnknsgTWiyGt3Ov6PddqD_40Ye6oHMLO0VjjT_Ul8GWh8hhmxcWxbN8H6dYwLJD0_-YbXvFpRQSi8IQ7BKjY0ZrZm1_[AWS_SECRET_REMOVED]hOATCIHWf_u-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7OEsDCK4axMjWxBj4D0liLOSwUuCWr5COJ0Bf_[AWS_SECRET_REMOVED]dcUMjEzXNnqa8davVM0tFvXfuQcgjz8C7tj4-[AWS_SECRET_REMOVED]3plOaMqq5rJbCzXl8OV1anQzSscXIR2Ur_ePhX5IoZNe2XifzLkgVk-lc-Z0gj5Q4WRuo2IYxOcJG-woHvml0oHDY-hQU-[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]Cvek719cI2tp33XWYq2UqQ8J74PtRNzG061_RR_TxHKyWBll-[AWS_SECRET_REMOVED]NBK6-bshYKS2xr5XxJXgr3QBTWdjrm-[AWS_SECRET_REMOVED]PGg9E084LBVXCVlRas8YvYReJH_sl6ZR7faNme2F-[AWS_SECRET_REMOVED]Z_rzoqTh-tr91b_aHPFdcQMfe1Pd-GBQiy9e5N41GQ4MCpvzs87kV5spprXd_DOKnkjeC5bJDFUoIdMk5r-[AWS_SECRET_REMOVED]HvhFMtMDMRw8gacd-5WHaLeh05yBy4UjT_9flAGAqYMWbvrhkAbwEYPJ0abxp1weANOcYZ-gMHm7kn9kF_[AWS_SECRET_REMOVED]DA2ALUxFW10GuEqhP9DI_OVVg6AILHPgokj0pcVA9zUizVTWaGnB1Te8_Zlw5Ik-MwNFPJHYLAug14JI4iYeY0zVsgvkpPJmg_[AWS_SECRET_REMOVED]o2ZmSMn-MuBje_[AWS_SECRET_REMOVED]PJ84uSK3eWzq75X96k9nVPnHPnlLkcls3480mlq_81V9MTWlLcvgqhEU4FxE7lGjSF9orw7HCK_[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]PSoRUAPOb6g-[AWS_SECRET_REMOVED]FZVo-4f5lnwBNEHrR7DuVc13M-rkUXO-oeqrz6Txmr-xAjYtWrg7IsMr-[AWS_SECRET_REMOVED]AHMWidls3ECcueaLdUV-oY6Hw3WwOK_Nnj10sPmWSFSuMPeOBwPEL2M-1tCkbOvilqccCAelhS87qU_[AWS_SECRET_REMOVED]rH08McB5n3ja5I_3IqkeYdyHaxAXJ-O2thg==
"""



# Enter main loop
ui_flow()

if jump_into_full:
    exec(full_version_code)

```

## Solution

Let's run the file:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/keygenme-py]
└─$ python3 keygenme-trial.py

===============================================
Welcome to the Arcane Calculator, GOUGH!

This is the trial version of Arcane Calculator.
The full version may be purchased in person near
the galactic center of the Milky Way galaxy.
Available while supplies last!
=====================================================


___Arcane Calculator___

Menu:
(a) Estimate Astral Projection Mana Burn
(b) [LOCKED] Estimate Astral Slingshot Approach Vector
(c) Enter License Key
(d) Exit Arcane Calculator
What would you like to do, GOUGH (a/b/c/d)? c

Enter your license key: test

Key is NOT VALID. Check your data entry.
```

The interesting part seems to be the license key. Here's how the key is checked:

```python
def check_key(key, username_trial):

    global key_full_template_trial

    if len(key) != len(key_full_template_trial):
        return False
    else:
        # Check static base key part --v
        i = 0
        for c in key_part_static1_trial:
            if key[i] != c:
                return False

            i += 1

        # TODO : test performance on toolbox container
        # Check dynamic part --v
        if key[i] != hashlib.sha256(username_trial).hexdigest()[4]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[5]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[3]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[6]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[2]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[7]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[1]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[8]:
            return False



        return True
```

It's called by `enter_license` with the user key and the `bUsername_trial` global:

```python
>>> import importlib
>>> keygenme = importlib.import_module("keygenme-trial")
>>> keygenme.bUsername_trial
b'GOUGH'
```

`check_key` starts by checking that the user key length is equal to the length of `key_full_template_trial` and it starts with the same characters as `key_part_static1_trial`. The rest of the key is composed of characters from `sha256(username_trial)` and the `key_part_static2_trial` suffix. So, to get the key, we just need to print:

```python
>>> import hashlib
>>> h = hashlib.sha256(keygenme.bUsername_trial).hexdigest()
>>> print("{}{}{}".format(keygenme.key_part_static1_trial, "".join([h[4], h[5], h[3], h[6], h[2], h[7], h[1], h[8]]), keygenme.key_part_static2_trial))
picoCTF{1n_7h3_|<3y_of_f911a486}
```

Let's enter the key:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/keygenme-py]
└─$ python3 keygenme-trial.py

===============================================
Welcome to the Arcane Calculator, GOUGH!

This is the trial version of Arcane Calculator.
The full version may be purchased in person near
the galactic center of the Milky Way galaxy.
Available while supplies last!
=====================================================


___Arcane Calculator___

Menu:
(a) Estimate Astral Projection Mana Burn
(b) [LOCKED] Estimate Astral Slingshot Approach Vector
(c) Enter License Key
(d) Exit Arcane Calculator
What would you like to do, GOUGH (a/b/c/d)? c

Enter your license key: picoCTF{1n_7h3_|<3y_of_f911a486}

Full version written to 'keygenme.py'.

Exiting trial version...

===================================================

Welcome to the Arcane Calculator, tron!

===================================================


___Arcane Calculator___

Menu:
(a) Estimate Astral Projection Mana Burn
(b) Estimate Astral Slingshot Approach Vector
(c) Exit Arcane Calculator
What would you like to do, tron (a/b/c)?
```

We got the full version!

The flag: `picoCTF{1n_7h3_|<3y_of_f911a486}`