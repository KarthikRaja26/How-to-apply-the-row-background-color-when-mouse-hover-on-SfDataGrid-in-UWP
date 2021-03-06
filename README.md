# How-to-apply-the-row-background-color-when-mouse-hover-on-SfDataGrid-in-UWP

## About the sample
This example illustrates how to apply the row background color when mouse hover on SfDataGrid in UWP platform.

By default, the SfDataGrid does not have a support to apply the row background colour when hover on the row in UWP platform. But we can do this by hosting the SfDataGrid into the custom StackLayout and then write the renderer for the custom stackLayout to detect the movement of the mouse pointer and apply the Background color whenever the mouse pointer moved over the Row of the SfDataGrid in PointerMoved Event of custom stackLayout and setting the previous background color to the row when mouse pointer is released from the current row.

```XAML
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataGridDemo"
             xmlns:data="clr-namespace:Syncfusion.Data;assembly=Syncfusion.Data.Portable"
             xmlns:sfgrid="clr-namespace:Syncfusion.SfDataGrid.XForms;assembly=Syncfusion.SfDataGrid.XForms"
             xmlns:sfPager="clr-namespace:Syncfusion.SfDataGrid.XForms.DataPager;assembly=Syncfusion.SfDataGrid.XForms"
             x:Class="DataGridDemo.MainPage">
    <ContentPage.Content>
        <local:CustomStackLayout Orientation="Vertical">
            <sfgrid:SfDataGrid x:Name="sfgrid" AllowResizingColumn="True" AllowSwiping="True">

            </sfgrid:SfDataGrid>
        </local:CustomStackLayout>

    </ContentPage.Content>
</ContentPage>
```
 

```C#
public class CustomStackLayout : StackLayout
    {
        public CustomStackLayout()
        {

        }

        protected override void LayoutChildren(double x, double y, double width, double height)
        {
            base.LayoutChildren(x, y, width, height);
        }
    }
```

In UWP Renderer
```C#
public class CustomStackLayoutRenderer : VisualElementRenderer<CustomStackLayout, Control>
    {

        int previousIndex;

        DataRowBase rowdata;

        VirtualizingCellsControl wholeRowElement;

        SfDataGrid grid;

        Xamarin.Forms.Point point;
        public CustomStackLayoutRenderer()
        {
            this.PointerMoved += CustomStackLayoutRenderer_PointerMoved;
            this.PointerReleased += CustomStackLayoutRenderer_PointerReleased;
        }

        private void CustomStackLayoutRenderer_PointerReleased(object sender, Windows.UI.Xaml.Input.PointerRoutedEventArgs e)
        {
            var pointerPoint = e.GetCurrentPoint(this);
            point.Y = pointerPoint.Position.Y;
            point.X = pointerPoint.Position.X;
            var rowColumnIndex = SfDataGridHelpers.PointToCellRowColumnIndex(grid, point);
            var model = (GridModel)grid.GetType().GetRuntimeProperties().FirstOrDefault(x => x.Name.Equals("GridModel")).GetValue(grid);
            rowdata = SfDataGridHelpers.GetRowGenerator(grid).Items.FirstOrDefault(x => x.RowIndex == rowColumnIndex.RowIndex);
            if (rowdata != null)
            {
                wholeRowElement = (VirtualizingCellsControl)rowdata.GetType().GetRuntimeFields().FirstOrDefault(x => x.Name.Equals("WholeRowElement")).GetValue(rowdata);
                wholeRowElement.BackgroundColor = Xamarin.Forms.Color.White;
            }
        }

        private void CustomStackLayoutRenderer_PointerMoved(object sender, Windows.UI.Xaml.Input.PointerRoutedEventArgs e)
        {
            var pointerPoint = e.GetCurrentPoint(this);
            point.Y = pointerPoint.Position.Y;
            point.X = pointerPoint.Position.X;
            var rowColumnIndex = SfDataGridHelpers.PointToCellRowColumnIndex(grid, point);
            var model = (GridModel)grid.GetType().GetRuntimeProperties().FirstOrDefault(x => x.Name.Equals("GridModel")).GetValue(grid);
            if (previousIndex == rowColumnIndex.RowIndex)
            {

                rowdata = SfDataGridHelpers.GetRowGenerator(grid).Items.FirstOrDefault(x => x.RowIndex == rowColumnIndex.RowIndex);
                if (rowdata != null)
                {
                    wholeRowElement = (VirtualizingCellsControl)rowdata.GetType().GetRuntimeFields().FirstOrDefault(x => x.Name.Equals("WholeRowElement")).GetValue(rowdata);
                    wholeRowElement.BackgroundColor = Xamarin.Forms.Color.Red;
                }
            }
            else
            {
                rowdata = SfDataGridHelpers.GetRowGenerator(grid).Items.FirstOrDefault(x => x.RowIndex == previousIndex);
                if (rowdata != null)
                {
                    wholeRowElement = (VirtualizingCellsControl)rowdata.GetType().GetRuntimeFields().FirstOrDefault(x => x.Name.Equals("WholeRowElement")).GetValue(rowdata);
                    wholeRowElement.BackgroundColor = Xamarin.Forms.Color.White;
                }
            }
            previousIndex = rowColumnIndex.RowIndex;

        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomStackLayout> e)
        {
            base.OnElementChanged(e);
            grid = this.Element.Children[0] as SfDataGrid;
            grid.GridTapped += Grid_GridTapped;
            point = new Xamarin.Forms.Point();

        }

        private void Grid_GridTapped(object sender, GridTappedEventArgs e)
        {
            wholeRowElement = (VirtualizingCellsControl)rowdata.GetType().GetRuntimeFields().FirstOrDefault(x => x.Name.Equals("WholeRowElement")).GetValue(rowdata);
            wholeRowElement.BackgroundColor = Xamarin.Forms.Color.Blue;
        }
    }

```

## <a name="requirements-to-run-the-demo"></a>Requirements to run the demo ##

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) or [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).
* Xamarin add-ons for Visual Studio (available via the Visual Studio installer).

## <a name="troubleshooting"></a>Troubleshooting ##
### Path too long exception
If you are facing path too long exception when building this example project, close Visual Studio and rename the repository to short and build the project.
