# MySQL Transaction Isolation Levels – Practical Assignment

## 🎯 Objective

Demonstrate how different MySQL transaction isolation levels affect data consistency and concurrency. Focus on identifying and explaining transactional anomalies such as dirty reads, non-repeatable reads, and deadlocks.

---

## 🧪 Tasks

### Task 1: Dirty Read (READ UNCOMMITTED)

- Create a scenario using two concurrent sessions where one session reads data modified but not yet committed by another session.
- Use the `READ UNCOMMITTED` isolation level.
- Record the output of both sessions.
  ![image](https://github.com/user-attachments/assets/65b264af-50f4-4b77-9f97-fa6a110075dc)

- Explain why the anomaly observed is considered a **dirty read**.
Брудне читання виникає, коли транзакція читає дані, які були змінені іншою транзакцією, але ще не зафіксовані.Це відбувається при рівні ізоляції READ UNCOMMITTED, який дозволяє читати нефіксовані дані, порушуючи таким чином ізоляцію транзакцій. Транзакція читає нефіксовані дані з іншої транзакції, що може призвести до неправильних або суперечливих результатів.
- What caused the observed anomaly?
В цьому випадку було зняко кошти з аккаунту з id = 1, але транзакція ще не завершилась, і в цей же час під час іншої транзакції, яка відбувається паралельно, можна побачити що гроші вже знялись, але ці змінні ще не зафіксовані в системі, і якщо винекне збій (системний збій, мережева помилка або інша проблема), і транзакція не відбудеться, то транзакція яка читає змінені але не закоміченні дані видасть невірний результат. Якщо інша транзакція прочитає змінені дані до того, як перша транзакція встигне їх зафіксувати, це може призвести до брудного читання.
**Points: 5**
- Why does the isolation level used permit it?
Read Uncommitted - найнижчий рівень ізоляції. На цьому рівні одна транзакція може читати ще не зафіксовані зміни, зроблені іншими транзакціями, що дозволяє брудні читання. На цьому рівні транзакції не ізольовані одна від одної, через що і відбувається **dirty read**.
---

### Task 2: Non-Repeatable Read (READ COMMITTED)

- Create a scenario where a transaction reads the same row twice but gets different results within the same transaction.
- Use the `READ COMMITTED` isolation level.
- Record the session output that demonstrates this behavior.
  ![image](https://github.com/user-attachments/assets/ee5cc518-5ce1-42af-a59a-48d2b7f95d0d)
![image](https://github.com/user-attachments/assets/14c8581c-43ad-4edc-b3ad-69a5bac103e5)

- Explain the concept of **non-repeatable read** using the observed results.
Non-repeatable read виникає, коли транзакція читає той самий рядок двічі і кожного разу отримує різне значення. Наприклад, ми бачимо на прикладі вище що транзакція читає дані. Через паралелізм, інша транзакція оновлює ті ж дані і фіксує їх. Тепер, коли транзакція перечитала ті ж дані, вона отримала інше значення.
**Points: 5**
- What caused the observed anomaly?
Non-repeatable read відбулося тому, що транзакція двічі прочитала один і той самий рядок і отримала різні значення. Це відбулось, бо між цими двома читаннями паралельна транзакція модифікувала і зафіксувала зміни в тому ж рядку. Це призвело до того, що транзакція отримала суперечливі результати в межах однієї транзакції.

Це порушує принцип repeatable read, згідно з яким дані, прочитані один раз, не повинні змінюватися протягом транзакції.
- Why does the isolation level used permit it?
Рівень ізоляції READ COMMITTED гарантує, що будь-які прочитані дані будуть зафіксовані в момент читання. Таким чином, він не дозволяє брудного читання - транзакція не може побачити незавершені зміни іншої транзакції. Але цей рівень ізоляції не гарантує non-repeatable read. Це означає, що якщо транзакція виконує кілька SELECT до одного й того ж рядка, вона може отримати різні результати, якщо інша транзакція встигне змінити й зафіксувати цей рядок між зчитуваннями.
Це можливо тому, що READ COMMITTED дозволяє бачити останній зафіксований стан бази даних, навіть якщо попередні запити в тій же транзакції вже читали ці ж дані. 
---

### Task 3: Explanation and Terminology

- For each of the above tasks, provide a brief written explanation using correct transaction terminology:
  - What caused the observed anomaly?
  - Why does the isolation level used permit it?
- Ensure that terms like "dirty read", "commit", "transaction", "concurrency" are used correctly.

**Points: 5**

---

## 🔁 Bonus Tasks

### Bonus Task 1: Repeatable Read (REPEATABLE READ)

- Design a scenario to demonstrate that once a transaction reads a row, any committed updates to that row by other transactions are not visible until the transaction ends.
- Use the `REPEATABLE READ` isolation level.
- Include the output and explanation.
![image](https://github.com/user-attachments/assets/9aff06ee-15f6-46ea-992b-7b7b12c81a02)
![image](https://github.com/user-attachments/assets/7a4e5373-f919-4303-acfc-75224b63039c)
Було використано рівень ізоляції Repeatable Read, і тому під час виконання двох транзакій паралельно, не було зміннно значення як в read committed, бо рівень ізоляції Repeatable Read найсуворіший. Транзакція тримає блокування на читання для всіх рядків, на які вона посилається, і записує блокування на рядки, на які вона посилається, для дій з оновлення та видалення. Оскільки інші транзакції не можуть читати, оновлювати або видаляти ці рядки, це дозволяє уникнути неповторного читання. 
**Bonus: +1 Point**

---

### Bonus Task 2: Non-Repeatable Read vs Repeatable Read

- Compare results of the same update scenario under `READ COMMITTED` and `REPEATABLE READ`.
- Clearly distinguish the behavior of each level in your explanation.
  Результат відрізняється, бо транзакція з рівнем ізолції READ COMMITTED було з Non Repeatable read, тобто транзакція двічі читає один і той самий рядок і кожного разу отримує різне значення.
Repeatable Read гарантує, що всі прочитані рядки залишаються незмінними протягом транзакції, це можна побачити в результаті, не виникло Non Repeatable read.
READ COMMITTED забезпечує базовий рівень ізоляції без dirty read, але дозволяє non-repeatable read.
REPEATABLE READ забороняє non-repeatable read, але зменшує рівень паралелізму через блокування.
**Bonus: +1 Point**

---

### Bonus Task 3: Deadlock

- Create a scenario involving two sessions that leads to a deadlock.
- Describe the steps taken and how MySQL responded.
- Capture and include any MySQL error or diagnostic output related to the deadlock.
- Explain why the deadlock occurred and how it was resolved.

**Bonus: +1 Point**

---

## 📊 Evaluation Table

| Task                                                                 | Points |
|----------------------------------------------------------------------|--------|
| Dirty Read under READ UNCOMMITTED                                    | 5      |
| Non-Repeatable Read under READ COMMITTED                             | 5      |
| Clear explanation with correct terminology (2 questions in the  end) | 5      |
| Bonus: Demonstrates REPEATABLE READ                                  | +1     |
| Bonus: Comparison of READ COMMITTED vs REPEATABLE READ               | +1     |
| Bonus: Demonstrates and explains deadlock                            | +1     |
| **Total**                                                            | **15 + 3 Bonus** |

---

## 📦 Submission Requirements

- SQL scripts or screenshots showing each task executed
- A document or markdown report explaining each scenario
- Clearly labeled sections for each task and bonus

### 🧠 Transaction Knowledge Check – 10 Questions

1. What is a **dirty read**, and under which MySQL isolation level can it occur?

2. How does the **READ COMMITTED** isolation level prevent dirty reads but still allow non-repeatable reads?

3. Define a **non-repeatable read** and describe how it can be reproduced in a MySQL session.

4. What is a **transaction** in MySQL, and why are transactions important in multi-user database environments?

5. What does the **COMMIT** statement do in a transaction, and how does it affect data visibility?

6. Explain how the **REPEATABLE READ** isolation level prevents non-repeatable reads. What behavior did you observe during testing?

7. Compare the behavior of **READ COMMITTED** and **REPEATABLE READ** when a row is updated during an active transaction. What differences did you observe?

8. What is a **deadlock** in MySQL, and how can it occur in a multi-session environment?

9. How does MySQL detect and resolve **deadlocks**? What error message is typically shown?

10. Why is it important to choose the appropriate **transaction isolation level** for your application?
