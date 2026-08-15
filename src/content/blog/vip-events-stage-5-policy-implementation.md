---
title: "Cybersecurity Capstone — VIP Events (Stage 5: Policy Implementation)"
description: "The final stage of the VIP Events cybersecurity proposal — user authentication policy via Conditional Access, network configuration policy for the web app via Azure Policy, and why both are tested in non-production first."
pubDate: 'Aug 18 2026'
tags: [cybersecurity, azure, entra-id, conditional-access, azure-policy, mfa, zero-trust]
---

# Cybersecurity Capstone — VIP Events (Stage 5)

This is Stage 5 — the final stage of the VIP Events cybersecurity capstone, closing it out with the policies that lock in everything built and validated in [Stage 4](/blog/vip-events-stage-4-testing-validation/). Here I define the security policies that actually govern user authentication and the web application's network configuration. I've drawn a deliberate line between **Conditional Access** and **Azure Policy**, because they solve different problems: Conditional Access governs *user sign-in* — who can authenticate, and under what conditions — while Azure Policy governs *Azure resource configuration* — how resources get deployed and secured. Enforcing MFA is a Conditional Access job, not an Azure Policy one, and getting that distinction right matters for the design.

## User authentication policy

User authentication and MFA enforcement belong to **Conditional Access (P1)**, not Azure Policy — Azure Policy simply can't compel a user to complete MFA at sign-in, that's Conditional Access's job alone. Here's what I'd put in place:

- **Require MFA for all users** — every user must complete multi-factor authentication when accessing company resources, satisfying the baseline Zero Trust requirement of verifying identity on every sign-in.
- **Stronger enforcement for privileged and executive accounts** — the CEO and any administrator accounts are the highest-value identities and are targeted with always-on MFA and tighter session controls. These accounts are the single most important to protect, as noted in Stage 3.
- **Block legacy authentication** — legacy protocols (which cannot enforce MFA) are blocked, closing the most common MFA-bypass vector.
- **Sign-in risk and user risk policies (P2, Identity Protection)** — risky sign-ins are challenged or blocked automatically, and compromised-credential detection triggers remediation. This adds continuous, adaptive verification rather than a one-time check.
- **Device compliance requirement** — access to corporate resources requires a compliant, Intune-managed device for managed users, while unmanaged transient/BYOD devices are restricted to app-protection (MAM) scope only, consistent with the Stage 1 design.

Conditional Access needs Entra ID P1, though, which this build doesn't have. So tenant-wide MFA is enforced through **Security Defaults** instead, requiring every user to register for and use MFA — the same enforcement I validated back in Stage 4. Security Defaults is all-or-nothing; the granular, role-specific policies above are the production upgrade path once P1/P2 gets licensed.

Azure Policy still has a part to play in the authentication posture, just at the *resource* layer rather than the sign-in layer — auditing and enforcing that Azure resources require secure authentication and configuration, flagging anything that permits anonymous access or exposes a management endpoint publicly. Azure Policy governs the estate's configuration; Conditional Access governs the users signing into it. They're complementary, not interchangeable.

## Network configuration policy for web applications

This one's a genuine Azure Policy job. I used Azure Policy to enforce a consistent, secure network configuration on the VIP Foods web application and its backend, so secure configuration is guaranteed by policy rather than left to whoever's doing the manual setup that day.

- **HTTPS-only enforcement** — the built-in policy *"App Service apps should only be accessible over HTTPS"* is assigned to the VIP Foods app's subscription, ensuring all traffic to the application is encrypted in transit and any HTTP request is rejected.
- **Restrict access to approved virtual networks** — Azure Policy enforces that the web app is reachable only from a predefined set of virtual networks, using VNet integration and private endpoints rather than a public endpoint. This ties directly to the Stage 1 design: the VIP Foods backend sits on the `server_infrastructure` zone (10.10.40.0/24), and restricting it to approved VNets enforces that isolation at the platform layer, so the application cannot be reached from outside its permitted network boundary.
- **Disable public network access** — where the app does not require public exposure, Azure Policy enforces that public network access is disabled, reducing the external attack surface to the minimum necessary.

Assigning it follows the standard flow: pick the built-in (or custom) policy definition, scope it to the VIP Events / VIP Foods subscription, set the parameters, and assign. Because policy applies at the subscription scope, it covers current and future resources alike — a newly deployed web app inherits the secure configuration automatically, no extra step required.

## Testing in a non-production environment

Both of these need validating in a non-production environment before they ever touch production. Policy changes carry real operational risk, and authentication policies especially can cause severe, immediate disruption if something's misconfigured.

**Risks of unvalidated policy implementation:**

- **Lockout of legitimate users** — an overly broad Conditional Access policy can block access for users who should have it, halting normal operations. A misconfigured MFA or block policy can lock out every user at once.
- **Administrator lockout** — the most serious failure mode: a Conditional Access policy that blocks or over-restricts admin sign-in can leave the tenant with no way back in. For this reason, a **break-glass (emergency access) administrator account is always excluded** from Conditional Access enforcement, so there is a guaranteed route back into the tenant if a policy misfires.
- **Application disruption** — a network policy that restricts the web app to the wrong virtual networks, or disables public access the app actually needs, can break legitimate connectivity and take the application offline.
- **Unintended blast radius** — because policies apply at scope (tenant or subscription), a single misconfiguration can affect every user or every resource simultaneously.

**Benefits of testing first, and the method:**

- **Conditional Access report-only mode** — new authentication policies are first deployed in report-only mode, which evaluates the policy against real sign-ins and logs what *would* have happened without actually enforcing it. This surfaces the real-world impact (who would be blocked, who would be challenged) before any user is affected, and is the correct way to stage a Conditional Access rollout.
- **Pilot groups and staged rollout** — policies are applied first to a small pilot group, then expanded in stages, so any issue is caught at small scale rather than tenant-wide.
- **Non-production subscription for Azure Policy** — network and resource policies are tested against a non-production subscription or test resources first, confirming the policy enforces the intended configuration without breaking legitimate access, before assignment to the production scope.
- **Early detection and stability** — testing catches misconfigurations and policy conflicts early, allowing correction before impact, and results in a smoother, more stable rollout that minimises disruption to VIP Events' operations.

Comprehensive testing, report-only staging, and a protected break-glass account together let VIP Events tighten its security posture through policy without ever risking the availability of the systems that policy is meant to protect.

Stage 5 completes the security framework by defining the policies that actually enforce it. User authentication and MFA run through **Conditional Access** (with Security Defaults standing in as the Free-tier equivalent in this build), clearly separated from **Azure Policy**, which enforces secure *resource* configuration — HTTPS-only access and virtual-network restriction for the VIP Foods web application. Both are staged through non-production testing and Conditional Access report-only mode, with a break-glass admin account protected against lockout, so the policies harden the environment without ever putting it at risk. Together with the network segmentation, identity model, role-based access, and testing from the earlier stages, this is the layered, Zero Trust posture the whole proposal set out to build — and it's where the VIP Events capstone ends.

---

*This is the final stage of a five-stage capstone. [Stage 1](/blog/vip-events-stage-1-company-requirements/) (company requirements), [Stage 2](/blog/vip-events-stage-2-aad-setup/) (Entra ID setup), [Stage 3](/blog/vip-events-stage-3-roles-and-access/) (roles and access), and [Stage 4](/blog/vip-events-stage-4-testing-validation/) (testing and validation) precede it.*
