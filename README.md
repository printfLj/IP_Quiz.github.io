# IP Quiz

This repository contains the code for a **VLSM & FLSM Calculator** and an **IPv6 Quiz**.

To get **IP Quiz** running smoothly, you need to understand how these files talk to each other. Think of it like a restaurant: **index.php** is the menu/table (Frontend), **api.php** is the waiter, and the other files are the specialized chefs in the kitchen.

---

## 1. `IpUtils.php` (The Randomizer)
This is a simple utility class. Its only job is to generate a valid starting point for students who don't want to type an IP address manually.

* **Logic:** It uses `rand(min, max)` to pick numbers within the valid ranges for Class A, B, and C.
* **Networking Rule:** It respects the first-octet rules (e.g., Class A starts between 1 and 126). 
* **Output:** It returns a string like `192.168.45.0`.

---

## 2. `SubnetCalculator.php` (The "Brain" for IPv4)
This handles the **VLSM (Variable Length Subnet Masking)** logic.

* **Sorting:** The first thing it does is `arsort($hostRequests)`. In CCNA, you **must** subnet for the largest requirements first to prevent overlapping or wasted space.
* **The Math:** To find the prefix, it uses: 
    $$2^n \ge (\text{hosts} + 2)$$
    It calculates the exponent $n$ (host bits) using `log()` and subtracts it from 32 to get the CIDR prefix.
* **The "Pointer" System:** It converts the IP address into a long integer (`ip2long`). This allows the code to simply add the block size (e.g., +64) to find the next network address.
* **Overflow Check:** It compares the current broadcast address to the maximum allowed address of the original network. If it goes over, it triggers the **Suggestion Engine**, which calculates the total space needed and tells the user which prefix would actually fit.

---

## 3. `api.php` (The Bridge)
Since the website is dynamic, you don't want the page to refresh every time a user clicks "Calculate." This file acts as an **API Endpoint**.

* **Request Handling:** It listens for two actions: `generate` or `calculate`.
* **JSON Communication:** It takes the input from the frontend, sends it to the PHP classes, and then packages the result into a **JSON** object (a format JavaScript understands easily).

---

## 4. `index.php` (The UI & Interaction)
This is the main interface the user sees. It uses **Tailwind CSS** for styling and **JavaScript (Fetch API)** to talk to the backend.

* **Modes:** Features a toggle to seamlessly switch between the "IPv4 Subnetting" calculator and the "IPv6 Quiz".
* **The Fetch Loop:** When "Calculate" is clicked, JavaScript gathers the IP, Prefix, and Hosts, then "posts" them to `api.php`.
* **Dynamic Rendering:** Instead of loading a new page, it stays on the same page and uses `.map()` to inject new rows into the HTML table based on the calculation results.
* **Error Handling:** If the backend returns an "error" (e.g., insufficient space), the JavaScript reveals a hidden alert box and shows the suggested prefix.

---

## 5. `Ipv6Helper.php` (The Hexadecimal Logic)
IPv6 logic is simpler in terms of host bits; it primarily focuses on incrementing the **SLA ID** (the 4th quartet).

* **Hex Conversion:** It uses `dechex()` to turn a normal number (like 1, 2, 3) into Hexadecimal (0, 1, 2... A, B, C).
* **String Padding:** It uses `str_pad()` to make sure the subnet ID is always 4 characters long (e.g., `000A` instead of just `A`), which is the standard format for IPv6.

---

> **Note on Performance:** 
> The core logic depends on the `ip2long()` and `long2ip()` functions. PHP treats IP addresses as 32-bit integers under the hood, which makes the math much faster and more accurate than trying to manipulate strings like "192.168.1.1".

By splitting the code this way, the project remains highly **Modular**. If you decide to change how you generate random IPs later, you only have to edit `IpUtils.php` without touching the rest of your app.
