---
layout: post
title: "Homelab and Tailscale"
date: 2026-01-21
---

I run a homelab based around a refurbished ThinkPad. I was going to rebuild an old Lenovo tower but the ThinkPad I bought to learn Linux seemed like a better option. Pi-hole for DNS, a personal cloud for files, and a Tailscale mesh network tying it all together. I can't say enough good things about Tailscale - it's some of the most useful software I've come across.

## Tailscale Rules

I used to think VPNs meant dedicated servers, port forwarding, certificates, and headaches. Tailscale made that irrelevant. Install it, authenticate in a browser, and your devices can talk to each other from anywhere. It builds a mesh network using WireGuard underneath, so devices connect directly rather than routing through some central server.

My ThinkPad has a stable IP on my tailnet. I can SSH into it from anywhere. I use it to access files, push updates, monitor projects, and sometimes just remote into my desktop when I'm away from home. It's basically always available to me now regardless of what network I'm on. I've SSH'd into my homelab from airplane wifi. That still feels like magic.

## What's Running

The ThinkPad hosts two main things: Pi-hole for DNS filtering and a personal cloud for file storage.

Once Tailscale was up, I pointed my whole network's DNS at Pi-hole. Every device, even on mobile data, now filters through it. I have a dashboard showing every DNS request on my network.

The personal cloud handles file sync across devices. I can access everything from anywhere through Tailscale's encrypted mesh network. Magic DNS means I type a hostname instead of memorizing IP addresses.

## The Phone as a Terminal

I bought a Samsung A15 to use as a mobile terminal. It runs Termux with a full Linux environment - bash, Python, nmap, curl, grep, and a proot Kali install for heavier tools. With Tailscale, I can SSH into my homelab from anywhere without port forwarding. I've used it to run reconnaissance remotely and send results back to the ThinkPad for analysis.

The phone also connects to hardware over USB-C: logic analyzer for debugging signals, Arduino terminal for serial output, USB console for networking gear. It's become my portable lab for both software and hardware.

## What I Learned

Setting this up taught me more than any networking tutorial I've watched. I learned a lot just from booting Linux on a ThinkPad and eventually turning it into a server I SSH into from anywhere.

The ThinkPad cost less than a year of cloud hosting. A cheap way to actually learn networking and Linux fundamentals.
