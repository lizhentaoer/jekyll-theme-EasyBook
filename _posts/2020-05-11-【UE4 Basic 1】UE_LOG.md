---
layout: post
title:  【UE4 Basic 1】UE_LOG
date:   2014-12-30 09:00:13
categories: jekyll update
permalink: /archivers/hello
---

# 【UE4 Basic 1】UE_LOG
### LogVerbosity.h
```
/**
 * Enum that defines the verbosity levels of the logging system.
 * Also defines some non-verbosity levels that are hacks that allow
 * breaking on a given log line or setting the color.
**/
namespace ELogVerbosity
{
    enum Type : uint8
    {
        /** Not used */
        NoLogging       = 0,
 
        /** Always prints a fatal error to console (and log file) and crashes (even if logging is disabled) */
        Fatal,
 
        /**
         * Prints an error to console (and log file).
         * Commandlets and the editor collect and report errors. Error messages result in commandlet failure.
         */
        Error,
 
        /**
         * Prints a warning to console (and log file).
         * Commandlets and the editor collect and report warnings. Warnings can be treated as an error.
         */
        Warning,
 
        /** Prints a message to console (and log file) */
        Display,
 
        /** Prints a message to a log file (does not print to console) */
        Log,
 
        /**
         * Prints a verbose message to a log file (if Verbose logging is enabled for the given category,
         * usually used for detailed logging)
         */
        Verbose,
 
        /**
         * Prints a verbose message to a log file (if VeryVerbose logging is enabled,
         * usually used for detailed logging that would otherwise spam output)
         */
        VeryVerbose,
 
        // Log masks and special Enum values
 
        All             = VeryVerbose,
        NumVerbosity,
        VerbosityMask   = 0xf,
        SetColor        = 0x40, // not actually a verbosity, used to set the color of an output device
        BreakOnLog      = 0x80
    };
}
```
### 使用UE4默认的LogTemp
```
UE_LOG(LogTemp, Warning, TEXT("Your message"));
```
### 如果想定义只在一个CPP文件中使用的Category，不希望被其他类使用，可以定义一个Static的Category
```
DEFINE_LOG_CATEGORY_STATIC(CategoryName, Log, All);
```
### 如果想定义一个‘Public’的Category，并且在全局生效，不管是static函数还是其他类都可以使用，就需要在头文件中声明一个Category，并在CPP中定义，每个用到的CPP文件都需要include该头文件
```
// in A.h
DECLARE_LOG_CATEGORY_EXTERN(CategoryName, Log, All) // 声明一个Category为extern，避免多个文件使用此头文件时重复声明
  
// in A.cpp
#include "A.h"
DEFINE_LOG_CATEGORY(CategoryName) // 定义该Category，全局仅需一份
  
// in B.cpp
#include "A.h" // 由于之前声明为extern，使用者引入头文件即可使用在A.cpp中的定义
```
### 如果想给某个类定义一个专属的Category
```
// in A.h
classA
{
    DECLARE_LOG_CATEGORY_CLASS(CategoryName,Log,All);
};
  
// in A.cpp
DEFINE_LOG_CATEGORY_CLASS(A,CategoryName);
```
### UE_LOG使用:
```
UE_LOG(CategoryName,Warning,TEXT("MyCharacter's Health is %d"), MyCharacter->Health );

UE_LOG(CategoryName,Warning,TEXT("MyCharacter's Health is %f"), MyCharacter->Health );

UE_LOG(CategoryName,Warning,TEXT("MyCharacter's Name is %s"), *MyCharacter->GetName() );
```
### UE_LOG实际上调用的是Windows SDK debugapi.h的debug
```
void FWindowsPlatformMisc::LocalPrint( const TCHAR *Message )
{
     OutputDebugString(Message);
}
```
