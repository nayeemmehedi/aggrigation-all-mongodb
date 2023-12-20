# aggrigation-all-mongodb

#### aggrigation stage:

# addFields : 
"নতুন ক্ষেত্র যোগ করা" , একটি নতুন ক্ষেত্র তৈরি করা হয়েছে যার নাম ' totalHomework', totalQuiz . 

                   {
                _id: 1,
                student: "Maya",
                homework: [ 10, 5, 10 ],
                quiz: [ 10, 8 ],
                extraCredit: 0
              }
              {
                _id: 2,
                student: "Ryan",
                homework: [ 5, 6, 5 ],
                quiz: [ 8, 8 ],
                extraCredit: 8
              }
              
aggrigation:

       db.scores.aggregate( [
               {
                 $addFields: {
                   totalHomework: { $sum: "$homework" } ,
                   totalQuiz: { $sum: "$quiz" }
                 }
               },
               {
                 $addFields: { totalScore:
                   { $add: [ "$totalHomework", "$totalQuiz", "$extraCredit" ] } }
               }
            ] )


#  $bucket (aggregation) : 
MongoDB এ বাকেট এগ্রিগেশন ব্যবহার করতে হলে, আপনি আপনার ডেটাবেসের ডেটাকে বিভিন্ন বাকেটে ভাগ করতে চান এবং প্রতি বাকেটের জন্য কিছু এগ্রিগেট ফাংশন প্রয়োজন। বাকেট এগ্রিগেশন MongoDB এ একটি পাইপলাইন অপারেটর, এটি একটি বা একাধিক পাইপলাইন অপারেটরের সমন্বয়ে কাজ করে যাতে আপনি প্রয়োজনীয় ডেটা বা তথ্য অনুসন্ধান করতে পারেন।..


    db.artists.insertMany([
      { "_id" : 1, "last_name" : "Bernard", "first_name" : "Emil", "year_born" : 1868, "year_died" : 1941, "nationality" : "France" },
      { "_id" : 2, "last_name" : "Rippl-Ronai", "first_name" : "Joszef", "year_born" : 1861, "year_died" : 1927, "nationality" : "Hungary" },
      { "_id" : 3, "last_name" : "Ostroumova", "first_name" : "Anna", "year_born" : 1871, "year_died" : 1955, "nationality" : "Russia" },
      { "_id" : 4, "last_name" : "Van Gogh", "first_name" : "Vincent", "year_born" : 1853, "year_died" : 1890, "nationality" : "Holland" },
      { "_id" : 5, "last_name" : "Maurer", "first_name" : "Alfred", "year_born" : 1868, "year_died" : 1932, "nationality" : "USA" },
      { "_id" : 6, "last_name" : "Munch", "first_name" : "Edvard", "year_born" : 1863, "year_died" : 1944, "nationality" : "Norway" },
      { "_id" : 7, "last_name" : "Redon", "first_name" : "Odilon", "year_born" : 1840, "year_died" : 1916, "nationality" : "France" },
      { "_id" : 8, "last_name" : "Diriks", "first_name" : "Edvard", "year_born" : 1855, "year_died" : 1930, "nationality" : "Norway" }
    ])

aggrigation :
      
      db.artists.aggregate( [
        // First Stage
        {
          $bucket: {
            groupBy: "$year_born",                        // Field to group by
            boundaries: [ 1840, 1850, 1860, 1870, 1880 ], // Boundaries for the buckets
            default: "Other",                             // Bucket ID for documents which do not fall into a bucket
            output: {                                     // Output for each bucket
              "count": { $sum: 1 },
              "artists" :
                {
                  $push: {
                    "name": { $concat: [ "$first_name", " ", "$last_name"] },
                    "year_born": "$year_born"
                  }
                }
            }
          }
        },
        // Second Stage
        {
          $match: { count: {$gt: 3} }
        }
      ] )



MongoDB এ বাকেট এগ্রিগেশন ব্যবহার করতে হলে, আপনি আপনার ডেটাবেসের ডেটাকে বিভিন্ন বাকেটে ভাগ করতে চান এবং প্রতি বাকেটের জন্য কিছু এগ্রিগেট ফাংশন প্রয়োজন। বাকেট এগ্রিগেশন MongoDB এ একটি পাইপলাইন অপারেটর, এটি একটি বা একাধিক পাইপলাইন অপারেটরের সমন্বয়ে কাজ করে যাতে আপনি প্রয়োজনীয় ডেটা বা তথ্য অনুসন্ধান করতে পারেন।

এটির জন্য একটি সাধারিত উদাহরণ দেখা যাক:

javascript
Copy code
db.yourCollection.aggregate([
  {
    $bucket: {
      groupBy: "$fieldToGroupBy",
      boundaries: [boundary1, boundary2, boundary3],
      default: "Other",
      output: {
        count: { $sum: 1 },
        averageValue: { $avg: "$valueToAverage" }
      }
    }
  }
])


এখানে:

groupBy: এটি ডেটা ভাগ করার জন্য ব্যবহৃত ক্রিটেরিয়া এবং এটি একটি ফিল্ড নাম হতে হবে।
boundaries: এটি বাকেটের সীমা দেয় এবং এটি একটি অ্যারে হতে হবে যেখানে সীমা প্রতিটি বাকেটের জন্য একটি মান হবে।
default: এটি ডেটা সীমা এবং বাকেটের মাঝে যে কোনও মানের জন্য একটি ডিফল্ট মান হতে পারে।
output: এটি একটি অবজেক্ট, যা প্রতি বাকেটের জন্য বিভিন্ন এগ্রিগেট ফাংশনের জন্য একটি ম্যাপ হয়ে থাকে।
উপরের উদাহরণে, ডেটা fieldToGroupBy অনুযায়ী বাকেটে ভাগ করা হয়েছে এবং প্রতি বাকেটে মৌলিক মান এবং গড় মান হিসেবে ডেটা সাথে একটি সাময়িক ফিল্ড যোগ করা হয়েছে।

আপনি আপনার বৈশিষ্ট্যগুলি এবং প্রয়োজনীয় অপারেশনের উপরে ভিত্তি করে আপনার এই বাকেট এগ্রিগেশন পাইপলাইনটি সাজাতে পারেন।


 # $group (aggregation):
 
 $group এগ্রিগেশন অপারেটর MongoDB এ ব্যবহৃত হয় ডেটা ভাগ করা এবং এগ্রিগেট করার জন্য। এই অপারেটরটি একটি কোনও ফিল্ড বা ফিল্ডগুলির উপরে ভিত্তি করে ডেটা গুলি একত্রিত করে সংজোড় করতে ব্যবহৃত হয়।

     db.yourCollection.aggregate([
      {
        $group: {
          _id: "$fieldToGroupBy",
          total: { $sum: "$valueToSum" },
          average: { $avg: "$valueToAverage" },
          max: { $max: "$valueToFindMax" },
          min: { $min: "$valueToFindMin" },
          count: { $sum: 1 }
        }
      }
    ])


 # $match (aggregation)
 Filters the documents to pass only the documents that match the specified condition(s) to the next pipeline stage.

    
     { "_id" : ObjectId("512bc95fe835e68f199c8686"), "author" : "dave", "score" : 80, "views" : 100 }
    { "_id" : ObjectId("512bc962e835e68f199c8687"), "author" : "dave", "score" : 85, "views" : 521 }
    { "_id" : ObjectId("55f5a192d4bede9ac365b257"), "author" : "ahn", "score" : 60, "views" : 1000 }
    { "_id" : ObjectId("55f5a192d4bede9ac365b258"), "author" : "li", "score" : 55, "views" : 5000 }

example :

    db.articles.aggregate(
    [ { $match : { author : "dave" } } ]
    );


$skip (aggregation):

