# IP Quiz

This repository contains the code for a purely client-side **VLSM & FLSM Calculator** and an **IPv6 Quiz**.

This app is fully functional without any backend and can be hosted on any static site provider like **Vercel**, **Netlify**, or **GitHub Pages**.

---

## `index.html` (The Entire Application)
Since the project was migrated to be 100% static, all of the backend PHP logic has been cleanly rewritten into JavaScript and placed directly within the single HTML file.

* **UI & Interactions:** The user interface is built with standard HTML and uses **Tailwind CSS** for styling.
* **IPv4 Subnet Calculator (`calculateSubnets`):** Calculates Variable Length Subnet Masking (VLSM) and Fixed Length Subnet Masking (FLSM) entirely in the browser. It determines required bit counts, handles IP math (`ip2long` and `long2ip`), and instantly updates the UI with the required subnets.
* **Random IP Generator (`generateForPrefix`):** Randomly generates a valid RFC-1918 starting address (e.g., `10.x.x.x`, `172.16.x.x`, `192.168.x.x`) dynamically inside the browser based on the prefix size.
* **IPv6 Quiz:** Tests users on IPv6 Address Compression and Decompression logic with immediate feedback.

---

> **Note on Performance:** 
> The core logic depends on custom `ip2long()` and `long2ip()` functions built in JavaScript using 32-bit bitwise shifts (`>>> 0`) to correctly treat IPs as unsigned integers. This makes the math blazing fast and completely eliminates the need for server-side processing.
