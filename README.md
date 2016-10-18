# QtToolWindowManager

A Qt5 compatible fork of libQToolWindowManager from QtSolutions.

Changes made:

* Added CMake build files
* Fixed several code pieces to compile with Qt5

# How to use it?

Here is a simple tutorial which covers the basics. The code assumes that TWM is placed onto the main window in the Qt Designer

```C++
// MainWindow.cpp
MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);

    // setup tool window manager
    connect(ui->toolWindowManager, SIGNAL(toolWindowVisibilityChanged(QWidget*,bool)), this, SLOT(toolWindowVisibilityChanged(QWidget*,bool)));

    // add all tool windows
    mD3D11Widget = new D3D11Widget(this, QSize(640, 480));
    mTableBrowser = new TableBrowser(this);

    addToolWindow("3D View",            mD3D11Widget);
    addToolWindow("Bluepring editor",   createBlueprintEditor());
    addToolWindow("Output",             new LogOutputWindow(this));
    addToolWindow("Properties",         new PropertyBrowser(this));
    addToolWindow("Project",            mTableBrowser);

    // restore window layout on startup
    on_actionRestoreToolWindowLayout_triggered();
}

MainWindow::~MainWindow()
{
    // save window layout on exit
    on_actionSaveToolWindowLayout_triggered();

    delete ui;
}

void MainWindow::addToolWindow(const QString& windowName, QWidget* window)
{
    window->setWindowTitle(windowName);
    window->setObjectName(windowName);

    QAction* action = ui->menuWindows->addAction(windowName);
    action->setData(mToolActions.size());
    action->setCheckable(true);
    action->setChecked(true);
    connect(action, SIGNAL(triggered(bool)), this, SLOT(toolWindowActionToggled(bool)));
    mToolActions << action;

    ui->toolWindowManager->addToolWindow(window, QToolWindowManager::LastUsedArea);
}

void MainWindow::toolWindowActionToggled(bool state)
{
    int index = static_cast<QAction*>(sender())->data().toInt();
    QWidget *toolWindow = ui->toolWindowManager->toolWindows()[index];
    ui->toolWindowManager->moveToolWindow(
        toolWindow,
        state ? QToolWindowManager::LastUsedArea : QToolWindowManager::NoArea
    );
}

void MainWindow::toolWindowVisibilityChanged(QWidget *toolWindow, bool visible)
{
    int index = ui->toolWindowManager->toolWindows().indexOf(toolWindow);
    mToolActions[index]->blockSignals(true);
    mToolActions[index]->setChecked(visible);
    mToolActions[index]->blockSignals(false);
}

void MainWindow::on_actionSaveToolWindowLayout_triggered()
{
    QSettings settings;
    settings.setValue("toolWindowManagerState", ui->toolWindowManager->saveState());
    settings.setValue("geometry", saveGeometry());
}

void MainWindow::on_actionRestoreToolWindowLayout_triggered()
{
    QSettings settings;
    restoreGeometry(settings.value("geometry").toByteArray());
    ui->toolWindowManager->restoreState(settings.value("toolWindowManagerState"));
}

void MainWindow::on_actionClearToolWindowLayout_triggered()
{
    QSettings settings;
    settings.remove("geometry");
    settings.remove("toolWindowManagerState");
}


```
