---
date: <%tp.date.now("YYYY-MM-DD")%>T<%tp.date.now("HH:mm")%>
tags:
  - Daily
cssclasses:
  - daily
  <% "- " + tp.date.now("dddd", 0, tp.file.title, "YYYYMMDD").toLowerCase() %>
---
# DAILY NOTE
## <% tp.date.now("dddd, MMMM Do, YYYY", 0, tp.file.title, "YYYYMMDD") %>
***

<%* if (moment().format('dddd') === "Monday") { -%> 
### Week Plan

Goals:

Mon: 
- DSU


Tue (MP): 
- DSU

Wed: 
- DSU
- LA Grooming

Thu: (MP)
- DSU

Fri:  
- DSU
- Social


---

<%* } -%>

### Meeting Notes


---

### Kiro Journal 
%% #kiro %%