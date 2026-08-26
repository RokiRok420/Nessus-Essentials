# End-to-End Vulnerability Assessment Using Nessus Essentials

Below is a practical enterprise-oriented workflow. I’m using current Tenable documentation where licensing, supported platforms, scan configuration, scheduling, reporting, and risk scoring can change between releases.

- Scope and safety: In an enterprise, obtain written authorization and an approved target list before scanning. A vulnerability scanner can generate substantial traffic and can disrupt fragile systems. Never treat a public IP range, third-party domain, or production asset as authorized merely because it is reachable.

1. Assessment Architecture

- A typical internal deployment looks like this:

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/8effd0af-a323-469f-963a-0f835c81ad22" />

For a distributed enterprise, place scanners where they have network visibility of the assets being assessed. Scanning through a firewall from an inappropriate location can produce incomplete results.

# 2. Prerequisites and Setup
Step 1 — Establish authorization

Before touching Nessus, document:

Who authorized the assessment
What networks/assets are in scope
What is explicitly excluded
Permitted scanning dates/times
Permitted scan intensity
Emergency/stop-scan contact
Where scan results may be stored
Who is allowed to see the results

Example scope:

    Assessment:
    Internal Vulnerability Assessment
    
    In Scope:
    10.10.10.0/24
    10.10.20.10
    10.10.20.15
    
    Excluded:
    10.10.10.1
    10.10.20.50
    10.10.30.0/24
    
    Window:
    Sunday 01:00–05:00
    
    Owner:
    Security Operations
    
    Emergency Contact:
    SOC on-call

This scope document is more important than the scan configuration itself.

# 3. System Requirements

- Tenable's current Nessus 10.12 documentation lists supported platforms including Windows 10/11, supported Windows Server versions, macOS 14/15/26, and multiple Linux distributions including Debian, Ubuntu, RHEL, Kali, Rocky, SUSE, and others. Architecture support varies by platform.

For an enterprise scanner, also consider:

- CPU
- RAM
- Disk space
- Network connectivity
- Number of simultaneous targets
- Number of simultaneous checks
- Location relative to targets
- Firewall rules
- DNS resolution
- NTP/time synchronization

Don't size the scanner solely according to the operating-system minimum. Scan concurrency and target count matter significantly.

# 4. Nessus Essentials Licensing

This is an important correction to older Nessus tutorials you may find online.

Current Nessus 10.12 documentation lists Nessus Essentials at a 5-host license limit. Hosts discovered by a host-discovery scan do not count toward the license limit, and a host that has not been scanned for 90 days no longer counts. The limit applies both to hosts in an individual scan and to unique hosts scanned cumulatively.

Therefore:

    Nessus Essentials
           |
           +-- Intended for limited/small-scale use
           |
           +-- 5 licensed hosts under current 10.12 documentation
           |
           +-- Not appropriate for a large corporate vulnerability program

For a real enterprise with hundreds/thousands of assets, use the appropriate commercial Tenable product/licensing rather than trying to work around Essentials' host limit.

Gotcha: Older documentation and tutorials may show a different Essentials limit. Don't rely on old screenshots or YouTube videos; check the documentation corresponding to your installed Nessus version.

# 5. Download Nessus

Use Tenable's official download page and select the package matching:

Operating system
CPU architecture
Nessus version

Tenable states that the package is selected by OS and processor, while the activation code determines the Nessus product being installed.
  https://www.tenable.com/products/nessus/nessus-essentials

<img width="1892" height="979" alt="image" src="https://github.com/user-attachments/assets/b3692688-6521-4f1d-a6a0-c09b4b50fa30" />

<img width="1895" height="979" alt="image" src="https://github.com/user-attachments/assets/4ea52d46-f55b-45cc-93dc-a6d542d2c4b9" />

<img width="1907" height="979" alt="image" src="https://github.com/user-attachments/assets/e938aca0-3d14-4ba8-b474-3603345854e6" />


# 6. Install on Windows
Step 1

Download the Windows installer.

It will typically be an installer executable.

Step 2

Double-click the installer.

Step 3

Follow the installation wizard:

<img width="612" height="480" alt="image" src="https://github.com/user-attachments/assets/35718c18-9b19-49f8-a5d3-0ff8914143f6" />

<img width="586" height="459" alt="image" src="https://github.com/user-attachments/assets/4d2557e2-cbe3-4949-88f9-d100964e6583" />

<img width="591" height="448" alt="image" src="https://github.com/user-attachments/assets/f8c83faa-b58a-4065-a24e-a1a6028372fd" />

<img width="607" height="438" alt="image" src="https://github.com/user-attachments/assets/c7ee5373-605d-4f88-9da2-e1d7e9f9ff28" />

Tenable's Windows installation procedure uses the InstallShield wizard and notes that a restart may be required.

# Step 4

Open the Nessus web interface:

https://localhost:8834

For a remote scanner:

https://<scanner-ip>:8834

<img width="1832" height="963" alt="image" src="https://github.com/user-attachments/assets/e5cce108-5e8d-41fd-b0e8-428b9f8301bc" />

<img width="1918" height="981" alt="image" src="https://github.com/user-attachments/assets/58037338-1790-443c-b74f-92ab1a890322" />

# 7. Install on macOS
Step 1

Download the .dmg package.

Step 2

Double-click the .dmg.

Step 3

Open:

Install Nessus.pkg
Step 4

Follow:

Continue
   ↓
License Agreement
   ↓
Agree
   ↓
Install

macOS may request administrator credentials during installation.

Step 5

After installation, open:

https://localhost:8834

Tenable also documents command-line installation using hdiutil and installer if you need an automated deployment.

# 8. Install on Linux

For Debian/Kali/Ubuntu:

Step 1

Download the appropriate .deb.

Step 2

Install:

- " sudo dpkg -i Nessus-<version>-debian6_amd64.deb " 
Step 3

Start Nessus:

- " sudo systemctl start nessusd "
Step 4

Verify:

- " sudo systemctl status nessusd "
Step 5

Open:

- " https://localhost:8834 "

Tenable documents this dpkg and systemctl start nessusd workflow for Debian/Kali/Ubuntu.

For RPM-based distributions, use the appropriate Tenable package and your distribution's package manager.

9. Verify the Nessus Service

On Linux:

- " sudo systemctl status nessusd " 

You want:

Active: active (running)

You can also verify the listener:

- " sudo ss -lntp | grep 8834 " 

Expected conceptually:

LISTEN ... :8834

10. Initial Nessus Configuration

Open:

https://localhost:8834

or:

https://<scanner-IP>:8834

The browser may warn about the certificate because the initial Nessus installation commonly uses a self-signed certificate.

For an enterprise deployment, don't simply ignore certificate-management requirements indefinitely. Put proper TLS/certificate management into the operational design.


11. Register Nessus Essentials

During initial setup:

Nessus Setup
      ↓
Select Nessus Essentials
      ↓
Registration
      ↓
Activation Code
      ↓
Create Administrator Account

If you need an activation code, Tenable's registration flow allows you to request one using your name and email. If you already have one, enter it directly. Then create the Nessus administrator account.

Important distinction

There are several concepts that get confused in tutorials:

Tenable account — your account with Tenable.
Activation code — activates the Nessus product/license.
Nessus administrator account — account used to log into the Nessus scanner.
Tenable cloud tenancy — relevant to Tenable cloud products; a standalone Nessus Essentials deployment is not the same thing as deploying a cloud vulnerability-management tenant.

Don't assume that registering Nessus Essentials creates an enterprise cloud tenancy.

<img width="643" height="808" alt="image" src="https://github.com/user-attachments/assets/c1eeff5c-6752-4536-9f41-05c84f4bd236" />

<img width="835" height="849" alt="image" src="https://github.com/user-attachments/assets/a8969d60-a9bd-457a-af28-15080e3483dc" />

<img width="621" height="787" alt="image" src="https://github.com/user-attachments/assets/521a6106-5685-41d6-bb2b-ccc25b7602f7" />

Activation code on your email
<img width="806" height="776" alt="image" src="https://github.com/user-attachments/assets/2909f89c-091f-4cb4-bcf7-38044399bf7e" />

<img width="819" height="754" alt="image" src="https://github.com/user-attachments/assets/81e98a84-005c-4c12-bc6c-ceebadc16aea" />

<img width="556" height="641" alt="image" src="https://github.com/user-attachments/assets/4d8794f8-3c01-4c2b-9592-31039fc0df64" />

<img width="654" height="628" alt="image" src="https://github.com/user-attachments/assets/c5656ab0-29da-4046-8c8b-fef5ae08a296" />
