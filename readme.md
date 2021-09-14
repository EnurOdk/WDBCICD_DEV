დარჩენილი
-----------------------
1. გიტლაბზე ბრენჩის დამერჯვა
2. გიტლაბზე ბრენჩის შექმნა სხვა ბრენჩიდან
3. ფუნქცია/პროცედურები ფაილურ სტრუქტურაში


გიტლაბის თასქების გადაწყვეტის ვარიანტები
--------------------------------    
    webhook
        . დოკერში Flask API, გიტლაბის ვებჰუკი იძახებს ენდპოინტს, ბრენჩის დამრჯვა და ბრენჩის შექმნა ხდება დოკერში ენდპოინტის გამოძახების დროს
    ci/cd pipline (
        jankins) https://docs.gitlab.com/ee/ci/quick_start/

ფუნქცია პროცედურების ფაილებში გატანის საჭიროება
---------------------------------
    [მიუხედავად იმის რომ pgquarrel პროცედურებს და ფუნქციებს ადარებს ეს შედარება არის სტრინგის სტრინგთან და არა diff-ის საშუალებით, იმისათვის რომ შეიქმნას კოდის დამერჯვის კონფლიქტები ბრენჩების დამერჯვის დროს, საჭიროა pgquarrel-შედარების დროს git-ის რეპოზიტორიში ყრიდეს ფუნქციების და პროცედურების კოდს (რის შემდეგაც git დამერჯვის დროს ავტომატურად შეადარებს) ხოლო განსხვავებების ფაილის გაშვების დროს ფუნქცია პროცედურები დაემატოს განსხვავებების ფაილებს და ერთად გატარდეს postgresql სერვერზე]


[შესრულებული]

pg-branch - ბრენჩის გარემო
----------------------
(pg-branch გარემო არის დროებითი სოლუშენი სადაც იმიტირებული იქნა გიტლაბზე შესასრულებელი თასქები [ბრენჩის დამერჯვა და ახალი ბრენჩის შექმნა], pg-dev დეველოპერის გარემოს ფუნქციონალის იმპლენეტაციის ხელის შესაწყობად, გიტლაბის დანერგვის შემდეგ pg-branch გარემო აღარ არის საჭირო)

server.bat - ფაილი უშვებს დოკერის კონტეინერის რომელიც ამზადებს ბრენჩინგის PostgreSQL-სერვერს
.config-ფაილში PG_BRANCH_EXPOSE=5436 განსაზღვრავს ლოკალკურ კომპიუტერზე ბრენჩინგის დატაბეიზ სერვერის დოკერის კონტენერიდან გამომავალ პორტს ხოლო PG_BRANCH_USERNAME=postgres PG_BRANCH_PASSWORD=1234 PG_BRANCH_PORT=5432 ავტორიზაციის პარამეტრებს

    ჰუკები:

    1. ახალი ბრენჩის შექმნა (+)
    -----------------------------
    [parent_branch]> git checkout -b [new_branch]
    ახალი ბრენჩის შექმნა ხდება მშობელი ბრენჩიდან, ახალი ბრენჩის შექმნის დროს იქმნება ბრენჩის შესაბამისი დატაბეიზი ბრენჩინგის სერვერზე და ახლად შექმნილ დატაბეიზში კეთდება მშობელი ბრენჩის შესაბამისი დატაბეიზის ასლი

    2. ბრენჩის დამერჯვა (+)
    --------------------------
    [target_branch]> git merge [source_branch]
    ბრენჩის დამრჯვა გულისხმობს დამერჯვის ტარგეტ ბრენჩის შესაბამის დატაბეიზში დასამერჯი ბრენჩის შესაბამისი დატაბეიზთან არსებული განსხვავებების ასასახი SQL სკრიფტის დაგენერირებას და დაგენერირებული სკრიფტის დამერჯვის ტარგეტ დატაბეიზში გატარებას (დამერჯვის ტარგეტი შეიძლება იყოს როგორც ბრენჩინგის სერვერზე არსბეული დატაბეიზი ასევე პროდაქშენის სერვერზე არსებული დატაბეიზი რომელიც შეესაბამება master ბრენჩს ან staging-ის შესაბამის სერვერზე არსებული დატაბეიზი რომელიც შეესაბამება staging-ის ბრენჩს) 


pg-dev - დეველოპერის გარემო
--------------------------------
დეველოპერის გარემო არის დოკერის კონტეინერი რომელშიც გამართულია:
. გიტლაბის ჰუკები პროექტისთვის და ასევე ბრენჩის გარემოს დროებითი ჰუკები (ჰუკები იმპლემენტირებულია python-ის სკრიფტებით)
. დაკომპილირებულია pgquarell-ის ჩვენს მიერ მოდიფიცირებული ვერსია
. გაშვებულია დეველოპერის PostgreSQL-სერვერი
. shell.bat უზრუნველყოფს კონტეინერის ბოლო ვერსიის ჩამოტანას რეპოზიტორიდან და დეველოპერის shell-ით უზრუნველყოფას, სადაც ხდება git-ის საშუალებით რეპოზიტორიზე მუშაობა


    ჰუკები:

    1. დაკომიტება (+)
    -------------------------
    [branch_name]> git commit --allow-empty -m "commit message"
    ჰუკი ამზადებს დეველოპერის ბრენჩის შესაბამსი დატაბეიზს და ბრენჩინგის სერვერზე ბრენჩის შესაბამის დატაბეიზს შორის ცვლილებების სკრიფტის გენერირებას და ინახავს კომიტის ჰეშით აღნიშნულ ფაილში იგივე ჰეშით კომიტის დასაფუშად რემოუთ რეპოზიტორიში (ფაილის წარუმატებლად დაგენერირების შემთხვევაში კომიტი არ ტარდება)

    2. დაპუშვა (+)
    --------------------
    [branch_name]> git push origin [branch_name]
    ჰუკი უშვებს კომიტის ჰეშის შესაბამის SQL ფაილს ბრენჩინგის სერვერზე, იგივე ბრენჩის შესაბამის დატაბეიზში, ფაილი წინასწარ მომზადებულია კომიტის ჰეშის შესაბამისი კომიტის დროს (ჰუკი ასევე კრძალავს მასტერ ან სტეიჯინგ ბრენჩში დაპუშვას (ამ ბრენჩებში მხოლოდ ხდება დამერჯვა გიტლაბის გარემოდან))

    3. ცვლილებების ჩამოტანა (+)
    --------------
    [branch_name]> git pull origin [branch_name]
    ჰუკი აგენერირებს განსხვავებას ბრენჩინგის სერვერზე ბრენჩის შესაბამის დატაბეიზსა და დეველოპერის სერვერზე ბრენჩის შესაბამის დატაბეიზს შორის და ატარებს დეველოპერის დატაბეიზში, ასევე ჰუკი ითვალისწინებს ბრენჩის შესაბამისი დატაბეიზის პირველად მომზადებას დეველოპერის სერვერზე (თუ ასეთი ჯერ არ არსებობს)

    4. ბრენჩზე პირველად გადასვლა (+)
    --------------------
    [branch_name]> git checkout [branch_name]
    ბრენჩზე პირველად გადასვლის დროს, იმ შემთხვევაში თუ დეველოპერის გარემოში არ არის ჩამოტანილი/მომზადებული ბრენჩის შესაბამისი დატაბეიზი, ჰუკს ახდენს დატაბეიზის ინიცირებას (ასეთი რამ საჭირო არის რემოუთ რეპოზიტორიში ახალი ბრენჩის გაჩენის დროს git fetch შემდეგ, რადგან git fetch არ აღძრავს არანაირ ჰუკს)

    5. ლოკალური ბრენჩის შექმნა (+)
    ---------------------------------
    [parent_branch]> git checkout -b [new_branch]
    ლოკალური ბრენჩი დეველოპერს საშუალებას აძლევს ლოკალურად შექმნას ახალი ბრენჩი, შემდეგ რემოუთ რეპოზიტორიში ატვირთვის პერსპექტივით

    6. ლოკალური დამერჯვა (+)
    -------------------
    [target_branch]> git merge [source_branch]
    ლოკალური დამერჯვის ჰუკი დეველოპერს საშუალებას რემოუთ რეპოზიტორიში ატვირთვის გარეშე დამერჯოს დეველოპერის გარემოში არსბეული ბრენჩები 
