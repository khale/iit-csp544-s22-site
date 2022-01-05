---
layout: home
title: CSP 544
nav_exclude: true
seo:
  type: Course
  name: System and Network Security
---

# {{ site.tagline }}
{: .mb-2 }
{{ site.description }}
{: .fs-6 .fw-300 }

{% if site.announcements %}
{{ site.announcements.last }}
[Announcements](announcements.md){: .btn .btn-outline .fs-3 }
{% endif %}

## Welcome!

This is the webpage for CSP 544: System and Network Security at 
[IIT](https://iit.edu).  

## Communication
We will be using [Discord](https://discord.gg/VBSynmB3) for course communication.

## Books

### Required
See [here](https://www.amazon.com/dp/1733003932/ref=cm_sw_em_r_mt_dp_U_BRJfEb9D10NPV). 

### Other Useful Books
- [Security Engineering](https://www.cl.cam.ac.uk/~rja14/book.html) by Ross Anderson
- [Hand-On Ethical Hacking and Network Defense](https://www.amazon.com/dp/1285454618/ref=cm_sw_em_r_mt_dp_U_9WJfEbPZYT9WP) by Simpson and Antill
- [The Hacker Playbook 2](https://www.amazon.com/dp/B01072WJZE?ref_=cm_sw_r_kb_dp_r.1fEbY7PCXQJ&tag=kpembed-20&linkCode=kpe) by Peter Kim
- [Hacking: The Art of Exploitation](https://www.amazon.com/dp/B004OEJN3I?ref_=cm_sw_r_kb_dp_1.1fEb4R99QTB&tag=kpembed-20&linkCode=kpe) by Jon Erickson
- [RTFM](https://www.amazon.com/Rtfm-Red-Team-Field-Manual/dp/1494295504/ref=sr_1_1?keywords=red+team+field+manual&qid=1578590253&sr=8-1) by Ben Clark


## Development Environment
We will primarily be using virtual machine images to set up vulernable
environments for you to exploit. Thus, in order to do the labs, you'll need to
set up a hypervisor/VMM on your machine to complete the labs. You should be
able to use [VirtualBox](https://www.virtualbox.org/), VMware, or [libvirt](https://libvirt.org/). We'll be using the [SEED Labs](https://seedsecuritylabs.org/) for
most of the class, but we will augment them with our own. You can see [here](https://seedsecuritylabs.org/lab_env.html) to
get set up for the labs.

## Tools
- [Metasploit](https://www.metasploit.com/): A widely used penetration testing framework written in Ruby
- [Kali Linux](https://www.kali.org/): A Linux distro aimed at ethical hacking and pentesting
- [Defuse](https://defuse.ca/online-x86-assembler.htm#disassembly): An online x86 disassembler
- [Godbolt](https://godbolt.org/): An online compiler explorer
- [pwntools](http://docs.pwntools.com/en/stable/): a Python exploitation framework, meant for CTFs
- [shtest](https://github.com/hellman/shtest): A shellcode tester
- [Wireshark](https://www.wireshark.org/): Widely used network packet capture and analysis tool
- [SET](https://www.trustedsec.com/tools/the-social-engineer-toolkit-set/): Social engineering toolkit
- [PTF](https://github.com/trustedsec/ptf/): Penetration tester framework
- [GEF](https://gef.readthedocs.io/en/master/): GDB wrapper for reverse engineering and exploit development
- [Radare2](https://rada.re/n/): Reverse engineering framework
- [strace](https://strace.io/): Linux system call tracer
- [ltrace](https://blog.packagecloud.io/eng/2016/03/14/how-does-ltrace-work/): Linux library call tracer
- [Shodan](https://www.shodan.io/): Vulnerability scanner
- [Hydra](https://sectools.org/tool/hydra/): brute force cracking for remote services
- [Mimikatz](https://attack.mitre.org/software/S0002/): Windows credential dumper
- [hashcat](https://hashcat.net/hashcat/): password cracker
- [American Fuzzy Loop](https://github.com/google/AFL): widely used software fuzzer
- [syzkaller](https://lwn.net/Articles/677764/): Linux kernel fuzzing

## Other Useful Links and Resources
This is a list of other resources that you might find useful for this class and
for doing work in the security area in general. Feel free to peruse them at
your own convenience.

### CTFs
- [CTFTime](https://ctftime.org/ctfs), a list of upcoming CTFs
- [Preparing for a CTF](https://www.cbtnuggets.com/blog/training/exam-prep/how-to-prepare-for-a-capture-the-flag-hacking-competition)
- [PicoCTF](https://picoctf.org/)
- [Smash the stack](http://www.smashthestack.org/wargames.html) wargames
- [Google's CTF](https://capturetheflag.withgoogle.com/#beginners/)
- OverTheWire [wargames](https://overthewire.org/wargames/)

### Practice and Other Links
- [Hack The Box](https://www.hackthebox.com/): A great hacking playground
- [Pwnable](https://pwnable.tw/) practice challenges
- [CTF Field guide](https://trailofbits.github.io/ctf/) from trail of bits
- [Phoenix](https://exploit.education/phoenix/): Exploit education
- [CTFLearn](https://ctflearn.com/): a learning platform for exploitation
- [MIT's security reading list](http://css.csail.mit.edu/6.858/2019/reference.html)
- Adam Doupe's [security course videos](https://www.youtube.com/channel/UCWA6pfcx4Ok4xsIA7Mkr39w/playlists)
- Github based [course](https://github.com/nnamon/linux-exploitation-course) on Linux exploitation
- SQL injection [cheat sheet](https://www.netsparker.com/blog/web-security/sql-injection-cheat-sheet/)
- NMAP [cheat sheet](https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/)
- Guide to [lsof usage](https://danielmiessler.com/study/lsof/)
- [vx underground](https://vxug.fakedoma.in/): compendium of malwares and related papers/code



