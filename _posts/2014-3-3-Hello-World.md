---
layout: post
title: Write Unit Test For Your RecyclerView Adapter
published: true
---

When customizing a RecyclerView adapter, it is useful to create a set of unit tests so you yourself or any other future developers
will not break existing functionalities while modifying your adapter.

There are many literature on writing unit tests, and most will make reference to MVP (Model-View-Presenter) or clean 
architecture designs. These are good. Writing testable code is always good. 

But there are times when you need to take over a piece of legacy code, and it dates way back MVP became a buzzword-of-the-day. 
One may be tempted to go through the uphill task of refactoring the legacy code to MVP first, then apply unit test. This approach is definitely good, but introduces a HUGE overhead if you just wants to dive into learning how to write unit tests. 

It is still possible to write unit tests without MVP structure.

Here, I will document what I did to write unit test for a change request in a RecyclerView adapter.

To keep things simple, we have a recycler view that have some title or text. The change request is to insert new content every three items in the recyler view - probably advertisement. To keep things simple we will not focus on the content of the item-to-insert. It will just be another text view. 

## Project Structure

Project consists of just a MainActivity.java file to set up our recycler view with test data.

MainActivity.java

{% highlight java %}
{% raw %}
    public class MainActivity extends AppCompatActivity
    {
        private RecyclerView rv;
        private RecyclerView.LayoutManager layoutManager;

        @Override
        protected void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            rv = findViewById(R.id.recyclerView);
            layoutManager = new LinearLayoutManager(this);
            rv.setLayoutManager(layoutManager);

            // Forming up the test data
            List<String> data = Arrays.asList(getResources().getStringArray(R.array.TestDataSet));
            MyAdapter myAdapter = new MyAdapter(data);

            // Populate recyclerView with adapter
            rv.setAdapter(myAdapter);
        }
    }
{% endraw %}
{% endhighlight %}


MyAdapter.java

{% highlight java %}
{% raw %}
public class MyAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder>
{
    List<String> data;

    public MyAdapter(List<String> dataSet)
    {
        this.data = dataSet;
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType)
    {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.normal_list_item, parent, false);

        NormalContentViewHolder viewHolder = new NormalContentViewHolder(view);

        return viewHolder;
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position)
    {
        ((NormalContentViewHolder)holder).textView.setText(data.get(position));
    }

    @Override
    public int getItemCount()
    {
        return data.size();
    }

    @Override
    public int getItemViewType(int position)
    {
        return super.getItemViewType(position);
    }

    public static class NormalContentViewHolder extends RecyclerView.ViewHolder
    {
        public TextView textView;

        public NormalContentViewHolder(View itemView)
        {
            super(itemView);
            textView = itemView.findViewById(R.id.textView);
        }
    }
}
{% endraw %}
{% endhighlight %}
	

So far, the code shows a straight forward simple recycler list. Build the app and we should see this.

![Screenshot_1520314417.png]({{site.baseurl}}/images/Screenshot_1520314417.png)


## The Change

We now have a requirement to add an image view every 3 text list item. We have also decided that we do not want to modify the dataset of MyAdapter class. Maybe there will be other classes that depends on the unmodified (no image view) dataset. So what do we propose to do?

## Strategy
We will modify MyAdapter class only, to allow it to return an image view instead of a text view every three items. Now instead of going head-first into changing the code, we shall employ a Test-Driven-Development approach. That means to say that we will develop test cases for MyAdapter class first.

We will need to test the functions inside MyAdapter class. These are the changes that will be required:

 - getItemCount()
 
	The number of items returns will need to include the inserted image views. For a list of 5 items, we return 6.
 
 - getItemViewType(int position)
 
	View type returned at the 3rd, 7th, 11th and so forth will need to be of image view type instead of text view.

 - onCreateViewHolder(ViewGroup parent, int viewType)
 
	We need to create the correct type of holder, either a TextView of ImageView.

 - onBindViewHolder(RecyclerView.ViewHolder holder, int position)
 
	Different binding methods for different view types.
    
## Create Unit Test for MyAdapter

In the navigation pane, locate the folder <project_name>(test). Unit tests are located in this folder. Right-click this and create a new class named MyAdapterTest.

![ScreenShot2018-03-06_151053.png]({{site.baseurl}}/images/ScreenShot2018-03-06_151053.png)

Create the following functions in MyAdapterTest class.

{% highlight java %}
{% raw %}
public class MyAdapterTest
{
    @Before
    public void setup()
    {
        
    }

    @Test
    public void test_getItemViewType()
    {
        
    }
}
{% endraw %}
{% endhighlight %}

When you run the test suite, the function setup() annotated with @Before will be called before every function annotated with @Test.

Therefore we will perform setup functions in the setup() function.

We want to test that getItemViewType returns a special inserted text view every 3 items. Let's create static constants in MyAdapter to identify these two views.

MyAdapter.java

{% highlight java %}
{% raw %}
public class MyAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder>
{
    public final static int NORMAL_VIEW = 1;
    public final static int INSERTED_VIEW = 2;

    /* code removed here for simplicity */
}
{% endraw %}
{% endhighlight %}

Create an instance of MyAdapter, populate with some test data.

{% highlight java %}
{% raw %}
public class MyAdapterTest
{
	MyAdapter adapterUnderTest;
    List<String> testData;
  
    @Before
    public void setup()
    {
        testData = new ArrayList<>();
        for (int i = 0; i < 20; i++)
        {
            testData.add("Item " + Integer.toString(i));
        }
        
        adapterUnderTest = new MyAdapter(testData);
    }

    @Test
    public void test_getItemViewType()
    {
        
    }
}
{% endraw %}
{% endhighlight %}

Create the test cases in test_getItemViewType() function.
For positions 0 to 2, it should return MyAdapter.NORMAL_VIEW. 
For position 3, it should return MyAdapter.INSERTED_VIEW.

{% highlight java %}
{% raw %}
public class MyAdapterTest
{
	MyAdapter adapterUnderTest;
    List<String> testData;
  
    @Before
    public void setup()
    {
        testData = new ArrayList<>();
        for (int i = 0; i < 20; i++)
        {
            testData.add("Item " + Integer.toString(i));
        }
        
        adapterUnderTest = new MyAdapter(testData);
    }

    @Test
    public void test_getItemViewType()
    {
        Assert.assertEquals(MyAdapter.NORMAL_VIEW, adapterUnderTest.getItemViewType(0));
        Assert.assertEquals(MyAdapter.NORMAL_VIEW, adapterUnderTest.getItemViewType(1));
        Assert.assertEquals(MyAdapter.NORMAL_VIEW, adapterUnderTest.getItemViewType(2));
        Assert.assertEquals(MyAdapter.INSERTED_VIEW, adapterUnderTest.getItemViewType(3));
    }
}
{% endraw %}
{% endhighlight %}

Run the test by clicking the icon located beside your test.

![ScreenShot_runTest.png]({{site.baseurl}}/images/ScreenShot_runTest.png)

You should get an error. That is no surprise because the correct function has not been implemented.

Now we return to our MyAdapter class to implement our getItemViewType such that the function will pass our test cases.

{% highlight %}
{% raw %}

/* portion of code hidden */

private final static int NUM_ARTICLES_BETWEEN_INSERTS = 3;

/* portion of code hidden */

@Override
public int getItemViewType(int position)
{
    if ((position + 1) % (NUM_ARTICLES_BETWEEN_INSERTS + 1) == 0)
    {
        return INSERTED_VIEW;
    }
    else
    {
        return NORMAL_VIEW;
    }
}

{% endraw %}
{% endhighlight %}

Re-run the test again and you should see the test passing.