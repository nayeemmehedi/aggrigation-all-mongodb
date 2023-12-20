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
         

