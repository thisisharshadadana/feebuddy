# FeeBuddy - my plan

## the idea
FeeBuddy is for home tutors in India. think of Priya, she teaches maths at home to around 15 kids. parents pay her every month, somewhere between 1500 and 2500 rupees, and honestly a lot of them pay late. she keeps everything in a notebook so she never really knows who owes her what, or if she'll have enough for rent.
so the app should let her keep her students, fees and reminders in one place, and show her how her money looks for the next 30 to 90 days.
the one line pitch: know who owes you, chase the right people, never be short on rent day.

## stuff that HAS to work
1. fee invoices every month, marking them paid, and ready-made reminder messages (friendly, firm, final, in English or Hindi) that open in WhatsApp
2. a dashboard that tells her who owes her, how much, and since when
3. a cash forecast for the next 30, 60 or 90 days using fees and upcoming bills
4. "what if" stuff, like what if a student pays 15 days late, or leaves, or she gets a surprise expense
5. alerts that tell her exactly what to do, like "chase Aarav today, 2000 rupees, 18 days late"
nice to have: a rent day meter, a bad day mode (when lots of people are late, tell her who to chase first so rent is covered), a small morning summary, quick entry where she just types "Aarav paid 2000", and payment habits for each family.

## tech (please stick to this)
- React 18 + Vite + TypeScript, strict mode
- Tailwind
- zustand with persist, saved in localStorage as "feebuddy-v1"
- date-fns for all the date stuff
- recharts for charts
- lucide-react for icons
- vitest for tests
- no backend and no login. it should work offline once it's loaded

## where things go
src/types.ts - all the types
src/lib/dates.ts - today() and date helpers
src/lib/money.ts - formatINR
src/lib/seed.ts - the sample data
src/lib/invoices.ts - making invoices and working out their status
src/lib/habits.ts - payment habits from past payments
src/lib/forecast.ts - the forecast maths
src/lib/scenarios.ts - the what-if maths
src/lib/alerts.ts - alerts, who to chase, the bad day plan
src/lib/reminders.ts - message templates and the WhatsApp link
src/lib/parser.ts - a simple parser for quick entry
src/lib/ai.ts - optional AI stuff with fallbacks
src/store/useStore.ts - the zustand store
src/components/ - shared bits of UI
src/screens/ - one file per screen

## the data
Settings: businessName, ownerName, upiId, openingBalance (number), balanceDate (date), language ('en' or 'hi'), demoToday (a date or null)
Student: id, name, parentName, parentPhone (10 digits), monthlyFee, dueDay (1 to 28), startDate, active, notes (optional)
Payment: id, date, amount, method ('upi' or 'cash')
FollowUp: id, date, tone ('friendly', 'firm' or 'final'), language ('en' or 'hi')
Invoice: id, studentId, period ('YYYY-MM'), amount, issueDate, dueDate, payments (list of Payment), followUps (list of FollowUp)
Bill: id, name, category ('rent', 'utilities', 'supplies' or 'other'), amount, recurring (true/false), dueDay if it repeats, date if it's one-off
CashEntry: id, date, amount (negative means money going out), note

cash in hand today = openingBalance (what she had at the start of balanceDate) + payments from balanceDate till today + cash entries from balanceDate till today - bills that came up between balanceDate and today.
things we work out instead of saving: invoice status (paid, partial or unpaid), amountDue (amount minus what's been paid), daysOverdue (days since the due date while something's still pending, otherwise 0).

## a few rules
- "today" always comes from today() in lib/dates.ts. it uses settings.demoToday if it's set, otherwise the real date. please don't use new Date() for today anywhere else
- money is whole rupees. show it with formatINR in the Indian way, like ₹1,25,000
- save dates as 'YYYY-MM-DD' strings
- keep the logic in src/lib as plain functions with tests. screens should just show stuff and call the store
- the app has to work completely without AI. AI is only there to make quick entry and reminder wording a bit nicer
- for forecasts say "expected" or "may", never "will" or "guaranteed"
- only fake sample data. phone numbers are fake and start with 90000

## how it should look (this matters a lot to me)
I want it to feel like the iPhone apps people use every day, like Apple Wallet or Health or Reminders. clean, calm, a bit premium, and super easy to get even if you're not a tech person.
- mobile first. it should look great at 390px wide, and on a laptop it's just a centred column, max 480px wide
- use the system font (-apple-system, BlinkMacSystemFont, "SF Pro Text", "Segoe UI", Roboto, sans-serif). body text 17px like iOS, and the important numbers big and bold, 34px or more
- every screen gets a big iOS style title (34px bold) that shrinks into a small title in a frosted bar at the top when you scroll
- background is the iOS grey #F2F2F7. cards are white, really rounded (rounded-3xl), with a very soft shadow. lists should look like the iPhone Settings app, grouped rows with thin lines between them and a little grey arrow on rows you can tap
- iOS colours: blue #007AFF for the main stuff, green #34C759 for paid or safe, orange #FF9500 for warnings or partial, red #FF3B30 for overdue or danger, grey #8E8E93 for small text. if I mention tailwind colours later like indigo, green, amber, red or slate, just use the matching iOS colour from here
- Home has one nice card at the top with a soft blue to indigo gradient showing her cash and the rent day meter
- bottom tab bar with 5 tabs (Home, Students, Forecast, Remind, More), frosted glass (white at 80% with backdrop blur), thin line on top, and a filled icon for the tab you're on
- a round + button floating on every screen that opens Quick entry
- bottom sheets like on iPhone: a little handle at the top, rounded corners, slides up smoothly
- main buttons are full width pill buttons, smaller actions are just blue text
- iOS style segmented controls for picking things like 30 / 60 / 90 or English / Hindi, and iOS toggle switches for on/off stuff
- every student gets a round avatar with their initials in a soft colour
- gentle animations, 200 to 300ms ease-out, cards shrink a tiny bit when you tap them (active:scale-[0.98]), and big numbers count up when they change. turn animations off if the phone asks for reduced motion
- tap areas at least 44px. one main thing per screen. little grey hints under anything that might be confusing
- every empty list gets a friendly message with an icon and a button
- simple words please. "Pending" not "Receivables", "Money coming in" not "Inflows"
- dark mode only if it's easy, it's just a bonus

## quality
- npm run build and npm test should pass with zero errors after every step
- no console errors and no 'any' types
- when we add something new, please don't break or rewrite stuff that already works
