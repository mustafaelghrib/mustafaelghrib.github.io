---
title: "أذاي أتعلمت ال DevOps وال Cloud"
date: 2023-05-12T19:18:02+03:00
description: 

---
## مقدمة
في المقال ده حبيت أتكلم عن أزاي أتعلمت ال DevOps وال Cloud وال Microservices مع نفسي ك self-study و أزاي أنت كمان تقدر تتعلمها؟ خصوصا إنها خدت مني وقت عشان أتعلمها لوحدي وإيه المصادر اللي ساعدتني وإيه المشاريع اللي عملتها خلال الرحلة دي.


## ليه أتعلمتهم؟
أول حاجة محتاجين نعرفها ليه أنا احتجت أتعلم الحجات دي أو إيه الدافع اللي خلاني أتعلمهم؟

أنا backend software engineer يدوب كنت بعرف أعمل backend APIs كان كل حاجة بعملها متعلقة بال code و أزاي أضيف feature أو أصلح bug ويوم ما أجي أعمل deployment كنت بجيب أي Ubuntu Server وباستخدام Nginx وبعمل الخطوات manually وخلاص على كده.

لحد ما بدأت أقدم على وظيفة جديدة ولاقيت معظم الوظايف طالبين الحجات دي خصوصاً لتخصص ال backend، الأسوء كان أني أترفضت في وظيفة بعد ما عديت أول انترفيو وبعتولي task اعمله فمعملتوش ذي ما متوقع بسبب أني مكنتش ملم بمواضيع ذي كده، ولما قريت عن ال modern software development lifecycle لقيت حجات كتير كانت ناقصني والمفروض أعرفها عشان أكون ملم بكل المواضيع أو ما يعرف بال T-shape person واللي هو ببساطة واحد عارف كل حاجة عن موضوع معين بس متخصص في جزء واحد، بالإضافة إن المجال بتاعنا محتاج تكون updated بكل جديد ومطور نفسك بأحدث ال technologies.

## أذاي أتعلمت بقى؟

إتباعاً لمفهوم ال T-shape وال modern software development lifecycle فبناء ال software بيتكون من عدة مراحل وكل مرحلة مهمة لتنفيذ المرحلة اللي بعدها، فلا مجال لإهمال مرحلة والبدء في أخرى، والمراحل دي بتتكون كالآتي:

- ال **planning** ودي مرحلة التخطيط وتعريف البرنامج وهيعمل إيه وإيه مميزاته ومين اللي هيستخدمه وهننفذه أذاي وهنستخدم tech stack إيه وإيه ال arch اللي هنبنيه بيها
- ال **designing** ودي المرحلة اللي بنصمم فيها البرنامج وبتشمل ال UX/UI design
- ال **coding** ودي بنبدأ ننفذ البرنامج بتاعنا سواء بقا backend او frontend او full stack
- ال **testing** ودي بنكتب فيها كل أنواع ال tests من unit tests ل integrated tests
- ال **building** دي المفروض ظهرت مؤخرا مع ظهور ال Docker وبتشمل building and packaging البرنامج بتاعنا بال code وال required packages في حاجة اسمها docker image وبنرفعها علي image registery بحيث نقدر نستخدمها بعد كده في هيئة containers
- ال **deployment** دي اتطورت مع ظهور ال cloud computing ومنها انبثق مجال ال DevOps ووصل لمراحل تطوره وتم اختراع أدوات جديدة عشان تسهل الموضوع ده

مع ال Agile المراحل دي بتتعمل مع كل turn أو المفروض بنقسم البرنامج ل sprints وكل sprint بنعمل المراحل دي وهكذا لحد ما نخلص البرنامج بتاعنا لأن صعب ننتظر كل مرحلة تخلص وندخل في اللي بعدها خصوصا لو البرنامج او النظام كبير فبنقسمه لحتت صغيرة وننفذه كأنه برنامج لوحده بيمر علي كل المراحل دي.

مع كل مرحلة من دول ليها أدواتها الخاصة والمفروض يبقى في حد أدني من المعرفة المفروض نعرفه مش لازم نعرف كل حاجة أو ما يسمي بال T-shape learning فمثلاً في ال planning المفروض نعرف يعني إيه Requirements Document وأذاي نكتبه ويعني إيه Agile وأذاي نستخدمها وحجات تانية في ال backend ذي ال Database Design Document وال API Design Document وفي ال designing نكون عارفين يعني إيه UX/UI Design ويعني إيه User Stories وفي ال coding المفروض ده تخصصنا ونبقى عندنا عمق معرفي فيه بكل الحجات الخاص بيه وفي ال testing نكون عارفين أذاي نكتب unit tests وأذاي نستخدم ال Test-Driven-Development (TDD) وبالنسبة لل building نكون عارفين يعني إيه Docker و Containerization وأذاي نعمل Custom Docker Images من ال code بتاعنا، أما بقى مرحلة ال deployment فدي اللي فيها ال heavy work ومش لازم نكون عارفين كل أدوات ال DevOps ولا كل ال Cloud Providers ولا كل ال CI/CD tools يعني على الأقل نكون عارفين cloud provider واحدة ذي AWS مثلا و Infrastructure as code (IaC) tool ذي Terraform و CI/CD tool ذي GitHub Actions.

## إيه المصادر اللي أتعلمت منها؟

أنا [كتبت بوست على لينكدان](https://www.linkedin.com/posts/mustafaabdulluh_i-recently-improved-my-skills-in-microservices-activity-7050936883920388096-_n3h/) فيه كل الكورسات اللي أخداتها والمشاريع اللي عملتها، بالإضافة لل Documentation بتاع كل الادوات دي، وكنت ببحث وأشوف فيديوهات على اليوتيوب للحجات اللي مكنتش فاهمها منها قنوات ذي [FreeCodeCamp](https://www.youtube.com/c/Freecodecamp) و [TechWorld with Nana](https://www.youtube.com/c/techworldwithnana) وقناة [باشمهندس محمد رضوان](https://www.youtube.com/channel/UC-G5jjBDFow_FRXql0t1JMQ) ومع ظهور ال ChatGPT بقى بيساعدني في حتة ال 
planning.

## الملخص
التعلم في مجالنا هيفضل مستمر مع تطور التكنولوجيا كل يوم، والأهم إننا نتعلم صح وال T-shape طريقة فعالة ومناسبة جداً لينا خصوصاً مع كل الحجات دي اللي مفروض نعرفها. أتمنى يكون المقال مفيد وتابعني لمقالات ذي كده أو لو عاوز تسألني على أي شئ متعلق بالموضوع أبعتلي على [لينكدان](https://linkedin.com/in/mustafaabdulluh).


