# Parallel

البته Task Parallel Libraray یا به اختصار TPL هم برای IO Bound و هم برای CPU Bound مناسب است

    public async Task Sample()
    {
        var customers = await GetCustomersFromSomeWhere();
    
        ActionBlock<Customer> action = new ActionBlock<Customer>(c => DoSomethingWithCustomer(c), new ExecutionDataflowBlockOptions
        {
            MaxDegreeOfParallelism = 25
        });
    
        foreach (var customer in customers)
        {
            action.Post(customer);
        }
    
        action.Complete();
    
        await action.Completion;
    }
توجه Parallel Linq و Parallel.ForEach  عموما برای کارهای CPU bound مناسبند.

توجه داشته باشید که اگر فرآیندی که برای تک تک Customer‌ها در مثال فوق قرار است انجام شود، خود یک روال سنگین و زمان بر باشد، بهتر است از روش‌های دیگری مبتنی بر Event processing و ابزارهایی چون Azure Service Bus یا Mass Transit استفاده کنیم که کمک می‌کنند اگر مثلا سه سرور داریم، بار پردازش آن 100 مشتری مثال ما، بین سه سرور هم پخش شود

مفهوم TPL (Task Parallel Library) یکی از جالب ترین ویژگی های جدید است که در نسخه های اخیر فریم ورک دات نت اضافه شده است. متدهای Task.WaitAll و Task.WhenAll دو روش مهم و پرکاربرد در TPL هستند.

در thread ,Task.WaitAll جاری تا لحظه تکمیل اجرای taskهای معرفی شده، متوقف می شود. در زمان استفاده از Task.WhenAll نیز تمام taskهای معرفی شده اجرا می شوند اما thread جاری برنامه متوقف نمی شود.

در اصل، Task.WhenAll به شما یک task را می دهد که کامل نیست، اما می توانید از ContinueWith به محض انجام کارهای مشخص شده استفاده کنید.

Task.WhenAll(taskList).ContinueWith(t => {
 // write your code here
});


WhenAny : مثال : گرفتن سرویس بلیط هواپیما از چند جا 
به صورت خلاصه در سی شارپ 5

- بجای task.Wait قدیمی، از await task برای صبر کردن تا پایان یک task استفاده کنید.
- بجای task.Result جهت دریافت یک نتیجه‌ی یک task از await task کمک بگیرید.
- بجای Task.WaitAll از await Task.WhenAll و بجای Task.WaitAny از await Task.WhenAny استفاده نمائید.
- همچنین Thread.Sleep در اعمال async با await Task.Delay جایگزین شده‌است.
- در اعمال غیرهمزمان همیشه متد ConfigureAwait false را بکار بگیرید، مگر اینکه به Context نهایی آن واقعا نیاز داشته باشید.
و برای ایجاد یک Task جدید از Task.Run یا TaskFactory.StartNew استفاده نمائید


نتیجه گیری : async / wait بهترین کار برای task های محدود به IO (ارتباطات شبکه ای ، ارتباطات پایگاه داده ، درخواست http و غیره) است. اما خوب نیست که درtaskهای محاسباتی اعمال شود (پیمایش لیست بزرگ ، پردازش یک تصویر بزرگ و غیره). زیراthreadنگهدارنده را از  thread pool  آزاد می کند و CPU/coreهای دردسترس، برای پردازش آن درtaskها به کارگرفته نمی شوند. بنابراین ، باید از استفادهAsync / Awaitبرایtaskهای محاسباتی جلوگیری شود.
برای پرداختن به  taskهای محاسباتی، بهتر است ازTask.Factory.CreateNew  باTaskCreationOptions  کهLongRunningاست، استفاده کنیم. با استفاده از این روش تا زمان   انجام  task، یکbackground threadجدید برای پردازش یک  taskسنگین محاسباتی بدون آزاد کردن مجدد آن ازthread pool، شروع می شود
