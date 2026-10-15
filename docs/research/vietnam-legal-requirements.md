# Vietnam legal requirements for a free online game

Research for issue #7. Law checked on 2026-10-15.

**Not legal advice.** This is a reading of the legal texts by a non-lawyer, written to inform the team's decisions. Before launching to the public, confirm with a Vietnamese lawyer or with the licensing authority.

## Answer

- **Every online game served to players in Vietnam needs a Vietnamese enterprise and a permit, free or not.** Decree 147/2024 sorts online games into G1 to G4 and lets only a "doanh nghiệp" (enterprise) release them: G1 needs a license plus a per-game release decision, G2 to G4 need a certificate plus a per-game release notice. Nothing in the decree exempts free, non-commercial or individual-run games.
- **Our game is G1.** Real-time play between people through our server is G1, the strictest class (licence, content appraisal per game). Bot-only play through our server alone would be G2.
- **A team of individuals cannot lawfully run it publicly for Vietnamese players.** Building, testing and private play among the team are not addressed by the licensing rules (unverified gray area). A public launch needs a registered company.
- **A licensed G1 game must do things that clash with the current plan.** It must verify every account with a Vietnamese mobile number (no guest play), have parents register accounts for under-16s, cap under-18 play at 60 minutes per game and 180 minutes per day, show a health warning every 30 minutes, show an age rating, moderate chat, and keep at least one server system in Vietnam.
- **Hosting in Singapore does not avoid licensing and adds data duties.** A Vietnamese company must store listed user data in Vietnam under the 2025 Cybersecurity Law and Decree 333/2026. Processing Vietnamese users' personal data on servers or LLM APIs abroad counts as a cross-border transfer under the Personal Data Protection Law, which requires an impact assessment filed with the Ministry of Public Security within 60 days, with no small-business exemption.
- **Online games are now managed by the Ministry of Culture, Sports and Tourism (MCST)**, through its Authority of Broadcasting and Electronic Information, since 1 March 2025.
- **AI features carry light duties.** Under the 2025 AI Law, tell users when they are talking to an AI (the Coach). Under the data protection rules, tell users about automated processing of their data and offer an opt-out. A Coach used by children must not exploit their vulnerabilities.
- **Rules are moving.** MCST is drafting an amendment to Decree 147 (consultation July 2026, not issued as of this check) that may tighten account verification and under-16 playtime.

## Sources and how they were read

Official portals (vanban.chinhphu.vn, vbpl.vn, thuvienphapluat.vn) blocked automated access or returned stubs during this check, so the full texts were read on mirrors that reproduce the official text: [hethongphapluat.com](https://hethongphapluat.com), [hrspring.vn](https://hrspring.vn) and [ketoananpha.vn](https://ketoananpha.vn). Mirrors can lag behind amendments. Where only press or law-firm summaries were available, the claim is marked **unverified**.

| Instrument | Status | Text read |
| --- | --- | --- |
| Decree 147/2024/NĐ-CP on Internet services and online information (9 Nov 2024) | In force since 25 Dec 2024; amendment in draft | [mirror](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang) |
| Decree 43/2025/NĐ-CP on MCST functions (28 Feb 2025) | In force since 1 Mar 2025 | [mirror](https://hethongphapluat.com/nghi-dinh-43-2025-nd-cp-quy-dinh-chuc-nang-nhiem-vu-quyen-han-va-co-cau-to-chuc-cua-bo-van-hoa-the-thao-va-du-lich) |
| Law 91/2025/QH15 on Personal Data Protection (26 Jun 2025) | In force since 1 Jan 2026 | [mirror](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/) |
| Decree 356/2025/NĐ-CP implementing the PDP Law (31 Dec 2025) | In force since 1 Jan 2026, replaces Decree 13/2023 | [mirror](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp), [official file](https://vbpl.moj.gov.vn/TW/Lists/vbpq/Attachments/187276/ND.356.2025.doc) |
| Law 116/2025/QH15 on Cybersecurity (10 Dec 2025) | In force since 1 Jul 2026, replaces the 2018 Cybersecurity Law and the 2015 Network Information Security Law | [mirror](https://hethongphapluat.com/luat-an-ninh-mang-2025.html) |
| Decree 333/2026/NĐ-CP implementing the Cybersecurity Law (19 Aug 2026) | In force since 19 Aug 2026 | [mirror](https://hethongphapluat.com/nghi-dinh-333-2026-nd-cp-huong-dan-luat-an-ninh-mang.html) |
| Law 134/2025/QH15 on Artificial Intelligence (10 Dec 2025) | In force since 1 Mar 2026 | [mirror](https://hethongphapluat.com/luat-tri-tue-nhan-tao-2025.html) |
| Decree 174/2026/NĐ-CP on administrative penalties (posts, telecoms, IT) | In force since 1 Jul 2026 | Not read; press summaries only |

Below, "D147 Art 39(1)(đ)" means Decree 147, Article 39, clause 1, point đ.

## 1. Who manages online games now

- MCST took over "thông tin điện tử" (electronic information) from the former Ministry of Information and Communications. Its listed tasks include "Quản lý về dịch vụ trò chơi điện tử trên mạng" (managing online game services) and issuing, suspending and revoking online game licences and certificates ([D43/2025 Art 1](https://hethongphapluat.com/nghi-dinh-43-2025-nd-cp-quy-dinh-chuc-nang-nhiem-vu-quyen-han-va-co-cau-to-chuc-cua-bo-van-hoa-the-thao-va-du-lich), Art 2(25)(a) and (b)), in force from 1 March 2025 (Art 4(1)).
- Decree 147 still names the old ministry: G1 licensing sits with "Cục Phát thanh, truyền hình và thông tin điện tử" (Authority of Broadcasting and Electronic Information, ABEI), and G2 to G4 notices with the provincial department ([D147 Art 38(3)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-38), [Art 52(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-52)). ABEI now sits under MCST and handles the G1 procedures, according to MCST's 2025 list of administrative procedures, Decision 682/QĐ-BVHTTDL ([summary](https://luatvietnam.vn/hanh-chinh/quyet-dinh-682-q-bvhttdl-2025-tthc-cac-linh-vuc-moi-thuoc-bo-van-hoa-the-thao-va-du-lich-394073-d1.html)). **Unverified:** which provincial department now receives G2 to G4 notices after the 2025 provincial mergers.
- Personal data protection is run by a unit of the Ministry of Public Security ([D356 Art 39(1)](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). The AI Law's notification portal is run by the Ministry of Science and Technology ([AI Law Art 10(3)](https://hethongphapluat.com/luat-tri-tue-nhan-tao-2025.html)).

## 2. Online game classification and licensing (Decree 147)

### Classes

[D147 Art 37(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-37):

| Class | Definition (short quote and gloss) | Our features |
| --- | --- | --- |
| G1 | "tương tác giữa nhiều người chơi với nhau đồng thời thông qua hệ thống máy chủ trò chơi của doanh nghiệp" (several players interact with each other at the same time through the enterprise's game server) | Real-time human vs human matches |
| G2 | "chỉ có sự tương tác giữa người chơi với hệ thống máy chủ" (players interact only with the server) | Bot games run on our server, server-side Coach |
| G3 | players interact with each other but not with the enterprise's server | Peer-to-peer play (not planned) |
| G4 | downloaded, no interaction with other players or the server | A bot running fully in the browser (our reading) |

Release rules ([D147 Art 37(2) to (4)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-37)):

- G1: the enterprise needs a "Giấy phép cung cấp dịch vụ trò chơi điện tử G1" (G1 service licence) **and** a "Quyết định phát hành" (release decision) for each game.
- G2, G3, G4: the enterprise needs a "Giấy chứng nhận" (service certificate) **and** a "Giấy xác nhận thông báo phát hành" (confirmation of release notice) for each game.
- Players may only play games that have a G1 release decision or a G2 to G4 confirmation ([D147 Art 69(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-69)).

No clause exempts free games, non-commercial games or classic board games. Since every class needs an enterprise, choosing an architecture (for example peer-to-peer G3) does not remove the need for a company.

### Who may provide a game

- An online game provider is defined as "doanh nghiệp thành lập theo pháp luật Việt Nam" (an enterprise established under Vietnamese law) ([D147 Art 3(35)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-3)).
- The G1 licence and the G2 to G4 certificate both require an enterprise with online games as a registered business line, a reachable head office, a registered domain, and "đủ khả năng tài chính, tổ chức, nhân sự" (adequate finances, organisation and staff) ([D147 Art 39(1)(a) to (c)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-39), [Art 48(1)(a) to (c)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-48)).
- Our inference: individuals and household businesses ("hộ kinh doanh") are not enterprises, so they cannot hold these permits. A sole proprietorship or an LLC would qualify as an enterprise.

### G1 licence conditions that shape the product

From [D147 Art 39(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-39) (the same list applies to G2 to G4 in [Art 48(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-48)):

- (d) A technical system connected to licensed payment providers, letting players look up their payment account. This applies even to a free game; how ABEI treats a game with no payments is **unverified**.
- (đ) Store each player's full name, date of birth and Vietnamese mobile number, and "xác thực tài khoản người chơi bằng số điện thoại di động tại Việt Nam, bảo đảm chỉ những tài khoản đã xác thực mới được tham gia chơi" (verify accounts with a Vietnamese mobile number; only verified accounts may play). For players under 16, a parent or guardian registers the account with the parent's details and supervises playtime and content.
- (e) Limit each player under 18 to 60 minutes per game and 180 minutes per day across all the provider's games (00:00 to 24:00).
- (g) Show the age rating continuously, and show "Chơi quá 180 phút một ngày sẽ ảnh hưởng xấu đến sức khỏe" (playing over 180 minutes a day harms your health) on screen every 30 minutes.
- (h) Moderate in-game chat and forums under Art 28(2).
- (i) Log account name, service time and virtual item ownership.
- (k), (l) Security, service quality, backup and contingency plans.

The licence lasts up to 10 years (Art 39(2)). A per-game release decision needs a licence with at least 6 months left, content that avoids the listed prohibited themes (including casino simulation and playing-card imagery), and a correct age rating; it lasts up to 5 years ([D147 Art 43](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-43)). G1 content is appraised with the help of an advisory council ([Art 55](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-55)).

### Ongoing provider duties

[D147 Art 54](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-54):

- (1) "Có ít nhất 01 hệ thống máy chủ đặt tại Việt Nam" (at least one server system located in Vietnam) for inspection, storage and handling complaints.
- (2) A website showing each game's age rating, rules, complaint rules, company details and licence numbers with a link to the ministry's licensing data.
- (3)(b) Only players who gave full, accurate details under Art 56(1) may play. Our reading: anonymous guest play is not allowed for a licensed game.
- (4) A unit that handles complaints and disputes, including between players.
- (6) 90 days' public notice before shutting down, and a report to the licensing authority.
- (8) No advertising before the release decision.
- (9), (10) Pay appraisal fees and file periodic reports. Fee amounts are **unverified**.
- (15) Child protection measures.

Player data: keep player details while they use the service and for 6 months after; let players refuse use of their data for marketing; be able to connect to the national population database to verify details when required ([D147 Art 56](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-56)).

### Age rating

Ratings are 18+, 16+, 12+ and 00+, self-assessed by the provider and checked during appraisal ([D147 Art 38](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-38)). Caro with no violence or sexual content looks like 00+ on the text, though 00+ describes games "mô phỏng dạng hoạt hình" (cartoon-style), which may matter for the visual direction. Open chat with strangers could push the rating up; **unverified**.

### Providers outside Vietnam

"Tổ chức, cá nhân nước ngoài cung cấp dịch vụ trò chơi điện tử trên mạng cho người sử dụng tại Việt Nam, gồm cả việc cung cấp dịch vụ xuyên biên giới, phải thành lập doanh nghiệp theo quy định của pháp luật Việt Nam" (foreign organisations and individuals serving online games to users in Vietnam, including cross-border, must set up a Vietnamese enterprise) ([D147 Art 37(4)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-37), repeated in [Art 23(1)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-23)). Authorities may apply technical measures (blocking) against foreign providers that break Vietnamese law ([Art 23(2)](https://hethongphapluat.com/nghi-dinh-147-2024-nd-cp-quan-ly-cung-cap-su-dung-dich-vu-internet-va-thong-tin-tren-mang/dieu-23)). So a company in Singapore or a server in Singapore does not avoid licensing.

### Penalties

**Unverified (press summaries only).** Decree 174/2026/NĐ-CP, in force from 1 July 2026, sets fines for online game violations. Reports say providers face fines up to 200 million VND for converting virtual items to cash and 60 to 80 million VND for some content violations (Art 99), and players get a warning for registering false personal details on G1 games (Art 101) ([luatvietnam](https://luatvietnam.vn/tin-van-ban-moi/khai-gian-tuoi-khi-dang-ky-tai-khoan-choi-game-bi-phat-canh-cao-tu-01-7-2026-186-109960-article.html), [lsvn](https://lsvn.vn/quy-doi-vat-pham-game-ra-tien-co-the-bi-phat-den-200-trieu-dong-a173378.html)). The fine for running a game without a licence was not found.

### Pending amendment to Decree 147

**Draft, not law.** MCST consulted on an amendment on 23 July 2026. Reported content: verify game and social accounts by "số điện thoại chính chủ hoặc mã số định danh cá nhân" (a phone number registered in the user's own name or a personal ID number), parents create and manage accounts for under-16s, a possible beta release for Vietnamese-made games before full licensing, and fewer administrative procedures ([luatnguyen, 28 Jul 2026](https://luatnguyen.vn/van-ban-sap-co-hieu-luc/sua-doi-nghi-dinh-147-buoc-ngoat-quan-ly-khong-gian-so-2433.html)). Press reports also mention a 60-minute daily cap for under-16s per game. No issued decree was found as of 2026-10-15.

## 3. Personal data protection (Law 91/2025 and Decree 356/2025)

- **Scope.** Applies to Vietnamese organisations and individuals, and to foreign ones that process data of Vietnamese citizens ([PDP Law Art 1(2)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). It applies to the team with or without a company.
- **Consent.** Valid only if voluntary and informed of the data types, purpose, the controller, and the data subject's rights. It must be given per purpose, cannot be bundled with unrelated purposes, and "Sự im lặng hoặc không phản hồi không được coi là sự đồng ý" (silence or no response is not consent) ([PDP Law Art 9](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). Consent must be verifiable and stored, the controller bears the burden of proof, and pre-ticked defaults are banned ([D356 Art 6](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). Processing needed to perform an agreement with the user does not need consent ([PDP Law Art 19(1)(d)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). A clear privacy notice covering Art 9(2) is the practical way to meet this.
- **Children.** A legal representative exercises a child's data rights. Processing a child's data to publish or reveal their private life needs consent from both the child (if 7 or older) and the representative ([PDP Law Art 24(2)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). Combined with Decree 147's parent-registered accounts for under-16s.
- **Impact assessment (DPIA).** Controllers file a processing impact assessment with the Ministry of Public Security within 60 days of starting ([PDP Law Art 21](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). Household businesses and micro-enterprises are exempt from Art 21, Art 22 and Art 33(2) (the DPO duty), and small enterprises and startups may opt out for 5 years from 1 Jan 2026, unless they process sensitive data or reach 100,000 data subjects ([PDP Law Art 38(2), (3)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/), [D356 Art 41](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). Individuals are not named in this exemption.
- **Cross-border transfer.** Counts as a transfer: using "nền tảng ở ngoài lãnh thổ" (a platform outside Vietnam) to process personal data collected in Vietnam ([PDP Law Art 20(1)(c)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)), including storing it on servers abroad or with a foreign cloud provider ([D356 Art 17(1)(a)](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). The sender files a transfer impact assessment (report on Form 09, transfer contract, policies) with the Ministry of Public Security within 60 days of the first transfer; the ministry answers within 15 days ([PDP Law Art 20(2)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/), [D356 Art 18](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). The small-business exemption in Art 38 does not cover Art 20, and Decree 356's added exemptions (press, emergencies, HR, travel and payments) do not cover a game ([D356 Art 17(3)](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)).
- **Basic personal data** includes name, date of birth, phone number, image and "tài khoản số" (digital accounts) ([D356 Art 3](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). Anonymous game records with no link to a person are outside this list.

## 4. Data localization (Cybersecurity Law 2025 and Decree 333/2026)

- Domestic and foreign enterprises providing Internet or value-added services in Vietnam must verify information at account registration, give user data to the Ministry of Public Security within 24 hours of a request (3 hours in emergencies), take down unlawful content within 24 hours, and keep logs ([Cybersecurity Law 2025 Art 25(2)](https://hethongphapluat.com/luat-an-ninh-mang-2025.html)). Account verification uses a Vietnamese mobile number, or a personal ID number or other legal e-ID for users without one, and only verified accounts may post or use interactive features ([D333 Art 16(2)](https://hethongphapluat.com/nghi-dinh-333-2026-nd-cp-huong-dan-luat-an-ninh-mang.html)).
- Enterprises that collect personal data, relationship data and user-generated data of users in Vietnam must "lưu trữ dữ liệu này tại Việt Nam" (store this data in Vietnam); foreign ones must also open a branch or representative office ([Cybersecurity Law 2025 Art 25(3)](https://hethongphapluat.com/luat-an-ninh-mang-2025.html)).
- Decree 333 splits this. "Doanh nghiệp trong nước lưu trữ dữ liệu quy định tại khoản 1 Điều này tại Việt Nam" (domestic enterprises store the listed data in Vietnam): personal information, account names, service times, card details, email, last login and logout IPs, and linked phone numbers ([D333 Art 19(1), (2)](https://hethongphapluat.com/nghi-dinh-333-2026-nd-cp-huong-dan-luat-an-ninh-mang.html)). Foreign enterprises in listed sectors, which include "trò chơi điện tử trên mạng" (online games), must do so only after a Minister of Public Security decision, issued when their service is used to break cybersecurity law and they fail to cooperate after 3 written requests over up to 6 months; they then have 12 months to comply ([D333 Art 19(3), (6)](https://hethongphapluat.com/nghi-dinh-333-2026-nd-cp-huong-dan-luat-an-ninh-mang.html)). Stored data is kept at least 24 months and system logs at least 12 months ([D333 Art 20](https://hethongphapluat.com/nghi-dinh-333-2026-nd-cp-huong-dan-luat-an-ninh-mang.html)). The form of storage in Vietnam is the enterprise's choice (Art 19(5)).
- These rules address "doanh nghiệp" (enterprises). Whether they bind a team of individuals is not stated; **unverified**.

## 5. AI features (AI Law 2025 and the data rules)

- **Scope.** Applies to Vietnamese and foreign organisations and individuals in AI activity in Vietnam ([AI Law Art 2](https://hethongphapluat.com/luat-tri-tue-nhan-tao-2025.html)). An "AI system" is a machine-based system "có khả năng tự thích nghi sau khi được triển khai" (able to self-adapt after deployment) that infers outputs from inputs (Art 3(2)). Our reading: a fixed search-based bot engine may fall outside this definition; an LLM-written Coach is more likely inside it. **Unverified.**
- **Risk tiers.** High, medium (systems that may confuse or manipulate users because they cannot tell they are dealing with AI or AI content) and low (everything else) ([AI Law Art 9](https://hethongphapluat.com/luat-tri-tue-nhan-tao-2025.html)). Providers self-classify; medium and high systems must notify the Ministry of Science and Technology before use (Art 10). Low-risk systems only owe explanations when authorities ask about a suspected violation (Art 15(2)).
- **Transparency.** Systems that interact directly with people must let users know they are interacting with AI; generated audio, images and video must carry machine-readable marks (Art 11(1), (2)). Deployers must label public AI content that could mislead about real events or people (Art 11(3)).
- **Prohibited.** Using AI to exploit vulnerable groups including children (Art 7(2)(c)), and using data for AI in breach of data protection law (Art 7(3)).
- **Transition.** Systems running before 1 March 2026 have 12 months to comply (Art 35). New systems comply from launch.
- **Personal data in AI.** Processing personal data by AI must be classified by risk ([PDP Law Art 30(4)](https://hrspring.vn/van-ban-phap-luat/91-2025-qh15-625628/)). Controllers must tell data subjects about automated processing, explain how the algorithm works and its effects, and "đưa ra các lựa chọn để chủ thể dữ liệu có quyền không tham gia" (offer a way to opt out), and must run a yearly compliance self-check ([D356 Art 10(3), (5)(đ)](https://ketoananpha.vn/nghi-dinh-356-2025-nd-cp)). Sending game records with personal identifiers to an LLM API abroad is a cross-border transfer (section 3); sending only anonymous move lists likely is not.

## 6. Options and trade-offs

These are options for later grilling tickets, not a decision.

### A. No company, individuals only

- Can do: design, build, test, and run private sessions among the team. Decree 147 governs providing a service to players; it does not address private development or closed tests. **Unverified gray area**, as there is no explicit exemption.
- Cannot lawfully do: open the game to the public in Vietnam, in any class (G1 to G4), with or without guest play.
- Still bound by: the PDP Law (consent, cross-border filing if any data goes abroad), since it covers individuals.
- Risks: fines under Decree 174/2026 (amounts for unlicensed games **unverified**) and blocking of the domain.

### B. Register a Vietnamese company and license the game as G1

- Gets a lawful path to the stated destination.
- Costs: a company, a G1 licence, a release decision per game (appraisal, fees, waiting time, all **unverified** in amount), and recurring reporting.
- Product changes: verified Vietnamese phone number for every account (guest play removed), parent-registered accounts for under-16s, playtime caps and health warnings for under-18s, age rating display, chat moderation, complaint handling, 90-day shutdown notice.
- Infrastructure changes: at least one server system in Vietnam (D147 Art 54(1)), and listed user data stored in Vietnam (D333 Art 19(2)). A Singapore-only deployment conflicts with this; options include hosting in Vietnam, or a split with user data and a server system in Vietnam and game servers in Singapore (the split's acceptability is **unverified**).
- Data: PDP DPIA likely exempt while micro and under 100,000 data subjects; cross-border filing still needed for anything processed abroad (Singapore servers, LLM APIs).

### C. Foreign entity or servers abroad, serving Vietnam anyway

- Decree 147 still requires a Vietnamese enterprise for foreign providers serving users in Vietnam, so this does not remove licensing.
- Foreign enterprises escape the default data localization duty (only on a Ministry decision), but face blocking under D147 Art 23(2).
- The team's members in Vietnam remain subject to the PDP Law.

### D. Narrow the audience or features

- Excluding Vietnam (geoblocking) would take the game outside Decree 147 but contradicts the destination ("real players in Vietnam"), and the laws of other Southeast Asian countries were not researched here.
- Dropping features does not remove licensing: bot-only server play is G2 and in-browser play is likely G4, and both still need an enterprise and a certificate.
- Feature choices that reduce duties: no open chat (less moderation and rating risk), no storage of real names or phone numbers beyond what licensing requires, anonymous data to the LLM, Coach clearly labelled as AI.

## Unverified or open

- Penalty amounts for running an online game without a licence (Decree 174/2026 text not read).
- Whether a private or closed test among friends counts as "providing" an online game service.
- How ABEI applies the payment-connection condition (D147 Art 39(1)(d)) to a free game.
- Which provincial department receives G2 to G4 notices after the 2025 mergers.
- Licensing fees and processing times.
- Whether a split deployment (Vietnam data plus Singapore game servers) satisfies D147 Art 54(1) and D333 Art 19.
- Whether a fixed bot engine is an "AI system" under the AI Law.
- The final content and date of the Decree 147 amendment.
