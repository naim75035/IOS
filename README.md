
// MainActivity.java package com.iweb.launcher;

import android.content.Intent; import android.content.pm.PackageManager; import android.content.pm.ResolveInfo; import android.os.Bundle; import android.view.View; import android.view.ViewGroup; import android.widget.BaseAdapter; import android.widget.GridView; import android.widget.ImageView; import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import java.util.List;

public class MainActivity extends AppCompatActivity { private PackageManager packageManager; private List<ResolveInfo> appList;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    packageManager = getPackageManager();

    Intent intent = new Intent(Intent.ACTION_MAIN, null);
    intent.addCategory(Intent.CATEGORY_LAUNCHER);
    appList = packageManager.queryIntentActivities(intent, 0);

    GridView gridView = findViewById(R.id.gridView);
    gridView.setAdapter(new AppAdapter());
}

private class AppAdapter extends BaseAdapter {
    @Override
    public int getCount() {
        return appList.size();
    }

    @Override
    public Object getItem(int position) {
        return appList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View view = getLayoutInflater().inflate(R.layout.grid_item, parent, false);
        TextView textView = view.findViewById(R.id.appName);
        ImageView imageView = view.findViewById(R.id.appIcon);

        ResolveInfo info = appList.get(position);
        textView.setText(info.loadLabel(packageManager));
        imageView.setImageDrawable(info.loadIcon(packageManager));

        view.setOnClickListener(v -> {
            Intent launchIntent = packageManager.getLaunchIntentForPackage(info.activityInfo.packageName);
            if (launchIntent != null) {
                startActivity(launchIntent);
            }
        });

        return view;
    }
}

}
