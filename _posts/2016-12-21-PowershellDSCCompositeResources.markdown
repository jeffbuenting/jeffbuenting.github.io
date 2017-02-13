---
layout: post
title:  "Powershell DSC Composite Resources"
date:   2016-12-21 11:23:00 -0400
categories: Powershell DSC
---
# Powershell DSC Composite Resources # 

## What are Composite Resources ##

PowersShell DSC lets you easily and quickly configure systems.  However the configuration files can grow quite complex and huge.  Sometimes many thousands of lines.  To make life easier for us humans to read, a large configuration can be broken up into smaller and reusable configurations.  These smaller configurations are called Composite Resources.  There syntax is the same as the standard or main configuration except they do not contain a 'Node' block.

## Composite Resource Folder Structure ##

The folder structure is very important and must be correct or the composite will not work.

ModuleName
	|
	----- DSCResources
	|         |
	|         ----- Resource
	|                   |
	|                   ----- Resource.psd1
	|                   |
	|                   ----- Resource.Schemal.psm1
	|
	----- ModuleName.psd1
	
Lets talk about each.

## How do you use Composite Resources ##

Enjoy

Jeff


