# 🎬 Netflix SQL Analysis

A comprehensive SQL-based analysis of **Netflix movies and TV shows**.  
This project explores content distribution, ratings, genres, and regional trends using SQL queries.

---

## 📊 Project Overview

This project involves analyzing Netflix's content dataset to extract valuable insights and answer key business questions such as:

- Distribution of **Movies vs TV Shows**
- Most common **ratings**
- Content trends by **year**, **country**, and **genre**
- Content categorization based on keywords like *violence* and *kill*

---

## 🎯 Objectives

1. Analyze the distribution of content types (**Movies vs TV Shows**)
2. Identify the most common **ratings**
3. Explore content by **year**, **country**, and **duration**
4. Retrieve insights on **directors**, **actors**, and **genres**
5. Categorize content based on specific **keywords**

---

## 🧱 Database Schema

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);

---

## 💡 Business Problems & SQL Solutions

---

### 1️⃣ Count the Number of Movies vs TV Shows

**Objective:** Determine the distribution of content types (Movies vs TV Shows) on Netflix.

```sql
SELECT 
    type,
    COUNT(*) AS total_count
FROM netflix
GROUP BY type;
