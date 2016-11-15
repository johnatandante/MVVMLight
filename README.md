# MVVMLight
A simple c# MVVM tool to bind ModelView class to their ViewModel xaml class

## HowTo Use
Just a quick reference guide:
1. Just Download the repo and include the project into your solution
2. Your model has to inherit the class ViewModel.cs
3. Use CustomCommand for implementing commands in your ViewModel

### Code Examples

#### ModelView
Replace `CustomNamespace.CustomControlClass` with the Control Class name you are going to implement.
Replace `CustomNamespace.CustomModelnamespace` with the ViewModel Namespace you are working in.
Replace `CustomClassViewModel` with the ViewModel Class you are going to implement in your ViewModel Namespace.

`<my:CustomClassViewModel x:Key="model"/>` is the static context reference we're pointing to
`CustomTextProperty` is the custom property (of type String) referenced in your ViewModel Class.
`CustomItemsSource` is the custom property (of type ICollectionView) referenced in your ViewModel Class.
`CustomSelectedItem` is the custom property (of any type) referenced in your ViewModel Class.
`DoSomethingCommand` is the custom command referenced in your ViewModel Class.

#### Xaml class - CustomNamespace.CustomControlClass.xaml
```
<UserControl x:Class="CustomNamespace.CustomControlClass"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        
        xmlns:my="clr-namespace:CustomNamespace.CustomModelnamespace">

    <UserControl.Resources>
        <my:CustomClassViewModel x:Key="model"/>
    </UserControl.Resources>
   <StackPanel>
     <Label Content="Write someting"/>
     <TextBlock Text="{Binding Source={StaticResource model}, Path=CustomTextProperty}" />
     <Label Content="Select someting"/>
     <ComboBox SelectedItem="{Binding Source={StaticResource model}, Path=CustomSelectedItem }"
               ItemsSource="{Binding Source={StaticResource model}, Path=CustomItemsSource}"/>     
     <Label Content="Do Something"/>
     <Button  Margin="6,0,6,0" Content="Esporta" Width="80"
                     Command="{Binding Source={StaticResource model}, Path=DoSomethingCommand}" />
   
   </StackPanel>
</Window> 
```

#### ViewModel 
In your model you should implement a context like this.
Note that in the constuctor you have to:
* Instantiate `DoSomethingCommand` with a new CustomCommand instance
* Instantiate `CustomItemsSource` with a generic IEnumerable, like `List<T>`

Also, don't forget to use `SetPropertyValue` protected method to notify listeners for binding update

#### C# class - CustomClassViewModel.cs

```
using *
...

namespace CustomNamespace.CustomModelnamespace
{
	public class CustomClassViewModel : MVVMLight.ViewModel
	{

    public string CustomTextProperty { get; set; }
    
    private string _CustomTextProperty = string.Empty;
		public string CustomTextProperty { 
      get { return _CustomTextProperty; }       
      set { SetPropertyValue<string>(ref _CustomTextProperty, value, "CustomTextProperty"); }  // TwoWay Binding
    }
    
    public object CustomSelectedItem { get; set; }    
		public ICollectionView CustomItemsSource { get; set; }
		public ICommand DoSomethingCommand { get; private set; }
    
    public PclToPdfViewModel() {
    
      this.DoSomethingCommand = new CustomCommand(() => {
				// do something 
			});
      
      CustomItemsSource = CollectionViewSource.GetDefaultView(new List<object>());
      
      // To apply binding
      CustomItemsSource.Refresh();
      
    }
    
```

## Remarks
Few remarks for this work:
1. Never tested on .Net Framework > 4 or .Net Core Framework
2. Requires xaml features and .Net Framework >= 3.5

