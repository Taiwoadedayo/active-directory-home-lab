# Active Directory Home Lab: Domain Setup and Role Based File Access

A hands-on lab where I built a Windows domain from scratch to practise the tasks an IT service desk handles every day: user accounts, password resets, security groups and file permissions.

## Objective

Understand how Active Directory works by building it myself, then use it to practise real service desk scenarios.

## Environment

| Component | Details |
|---|---|
| Hypervisor | Oracle VirtualBox |
| Server | Windows Server 2022 (domain controller + DNS) |
| Client | Windows 11 Pro |
| Domain | `Taiwo.local` |
| Network | VirtualBox Internal Network (`labnet`) |
| Server IP | `192.168.56.10` (static) |
| Client IP | `192.168.56.20` (static, DNS pointed at the server) |

## What I Built

1. **Configured networking:** set a static IP on the server and pointed its DNS to itself, since Active Directory depends on DNS.
2. **Installed AD DS:** added the Active Directory Domain Services role through Server Manager.
3. **Promoted the server to a domain controller:** created a new forest (`Taiwo.local`) with DNS and Global Catalog.
4. **Organised the directory:** created an Organizational Unit (`taiwo staff`), user accounts and a security group (`Helpdesk-Team`).
5. **Practised service desk tasks:** reset passwords, disabled and re-enabled accounts, and added users to groups.
6. **Created a restricted shared folder:** shared `C:\shares\helpdesk` and limited access to `Helpdesk-Team` using both share permissions and NTFS permissions (Modify).
7. **Joined a Windows 11 client to the domain:** logged in with a domain account and accessed the share over the network.

## Testing

| Test | Expected | Result |
|---|---|---|
| User in `Helpdesk-Team` opens `\\WIN-SERVER02\helpdesk` | Access granted | Pass |
| User in the group creates a file in the share | Allowed (Modify) | Pass |
| User not in the group opens the share | Access denied | Pass |
| Blocked user added to the group, signs out and back in | Access granted | Pass |

## Troubleshooting Log

| Problem | Cause | Fix |
|---|---|---|
| Server and client could not communicate | Both VMs were on NAT, which isolates each VM | Moved both to the same VirtualBox Internal Network |
| Domain join failed with a duplicate name error | Client computer name matched the domain's NetBIOS name | Renamed the client to `CLIENT01` and rejoined |
| Share path returned "No items match your search" | Path was typed into the search box instead of the address bar | Used Win + R to open the UNC path |

## Key Takeaways

- Access should be controlled through **groups**, not individual users.
- Most "I can't open this folder" tickets come down to **group membership**.
- Group membership changes only apply after the user **signs out and back in**.
- Share permissions and NTFS permissions work together, and the stricter one wins.
- DNS is critical: a domain-joined client must use the domain controller for DNS.

## Sample Service Desk Ticket

**Ticket:** User cannot open the Helpdesk shared folder.
**Cause:** User is not a member of `Helpdesk-Team`.
**Resolution:** Added the user to the group in Active Directory Users and Computers, asked them to sign out and back in, and confirmed access to the folder.

